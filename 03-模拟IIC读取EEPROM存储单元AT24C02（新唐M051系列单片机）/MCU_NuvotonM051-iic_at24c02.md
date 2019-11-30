## AT24C02的单字节读写-新唐M051系列

AT24C02电子档资料链接👉[传送门](https://pdf1.alldatasheet.com/datasheet-pdf/view/56063/ATMEL/AT24C02.html)

详细理解描述可以看这位老哥👉[传送门](https://www.cnblogs.com/whik/p/6650092.html)

------
[toc]
------

本文代码需要的声明和宏定义如下：

```c
#define IIC_SDA E_PORT1,E_PIN5
#define IIC_SCL E_PORT1,E_PIN6

#define IIC_SDA_SET DrvGPIO_SetBit(IIC_SDA)
#define IIC_SDA_CLR DrvGPIO_ClrBit(IIC_SDA)

#define IIC_SCL_SET DrvGPIO_SetBit(IIC_SCL)
#define IIC_SCL_CLR DrvGPIO_ClrBit(IIC_SCL)

#define SDA_READ DrvGPIO_GetBit(IIC_SDA)

typedef unsigned char u8;
typedef unsigned short u16;
typedef unsigned long u32;
```

### IIC协议起始和终止

![img](https://img2018.cnblogs.com/blog/1269466/201903/1269466-20190314191437921-877173367.png)

游戏**起始**的规则：数据线（SDA）在时钟线（SCL）**保持在高位**的情况下产生变化：高→低

游戏**终止**的规则：数据线（SDA）在时钟线（SCL）**保持在高位**的情况下产生变化：低→高

代码如下（延时一般保证大于4.7us就行，可稍微大些）：

```c
void IIC_Start(void)
{
　　IIC_SDA_SET;
　　IIC_SCL_SET;
　　DrvSYS_Delay(8);
　　IIC_SDA_CLR;
　　DrvSYS_Delay(8);
　　IIC_SCL_CLR;
}

void IIC_Stop(void)
{
　　IIC_SCL_CLR;
　　IIC_SDA_CLR;
　　DrvSYS_Delay(8);
　　IIC_SCL_SET;
　　DrvSYS_Delay(8);
　　IIC_SDA_SET;
}
```

### IIC协议等待响应

```c
void IIC_Wait_Ack(void)
{
　　u8 i=0;

　　IIC_SDA_SET;
　　DrvSYS_Delay(8);

　　IIC_SCL_SET;
　　DrvSYS_Delay(8);
　　while(SDA_READ&&i<200)
　　{
　　　　i++;
　　　　SendString("ack fail \n");
　　}    
　　IIC_SCL_CLR;//<--------这里千万不能忘，漏掉读出来的全都是255
　　DrvSYS_Delay(8); 
}
```

### IIC协议发送一个字节

```c
void IIC_Send_Byte(u8 byte)
{
    char str[20];
    u8 i;
    
    for(i=0;i<8;i++)
    {
        IIC_SCL_CLR;
        DrvSYS_Delay(8);
        
        if(byte&(1<<(7-i)))
            IIC_SDA_SET;
        else
            IIC_SDA_CLR;
        
        DrvSYS_Delay(8);

        IIC_SCL_SET;
        DrvSYS_Delay(8);

    }
        IIC_SCL_CLR;
        IIC_SDA_SET;
        DrvSYS_Delay(8);
        sprintf(str,"--%x--",byte);
        SendString(str);
}
```

### IIC协议接收一个字节

```c
u8 IIC_Read_Byte(void)
{
    char str[22];
    u8 i,dat=0;
    IIC_SDA_SET;
    IIC_SCL_CLR;
    DrvSYS_Delay(8);
    for(i=0;i<8;i++)
    {
        IIC_SCL_SET;
        DrvSYS_Delay(8);
        dat<<=1;
        
        dat|=SDA_READ;
        DrvSYS_Delay(8);
        
        IIC_SCL_CLR;
        DrvSYS_Delay(8);
        sprintf(str,"--%d--",dat);
        SendString(str);
    }
        
    return dat;
}
```

*以上是IIC协议的部分，下面是对AT24C02进行操作*

### MCU引脚初始化

首先模拟的IIC需要对两时钟和数据线连接的引脚进行配置，时钟线让它保持输出就行，数据线则需要设置为准双向，代码如下：

```c
void IIC_Init(void)
{
    DrvGPIO_Open(IIC_SDA, E_IO_QUASI);
    DrvGPIO_Open(IIC_SCL, E_IO_OUTPUT);
}
```

### 向EEPROM中写数据

```c
u8 AT24C02_ReadOneByte(u16 ReadAddr) 
{ 
    u8 temp=0; 
    IIC_Start(); 

    IIC_Send_Byte(0XA0); //发送器件地址0XA0,写数据
    IIC_Wait_Ack();
    IIC_Send_Byte(ReadAddr); //发送低地址
    IIC_Wait_Ack(); 
    IIC_Start(); 
    IIC_Send_Byte(0XA1); //进入接收模式 
    IIC_Wait_Ack(); 

    temp=IIC_Read_Byte(); 
    IIC_Stop();//产生一个停止条件 

    return temp;
}
```

 