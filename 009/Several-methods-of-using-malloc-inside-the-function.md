### 函数内部使用malloc的几种方法

#### 需求

最近碰到一个需要在函数内部进行动态内存分配的需求，比如：

```c
void func1(char *p)
{
    int n;
    //...		给n赋值
    p = (char *)malloc(sizeof(char)*n);
    //...		向*p写数据
}
int main(int argc, char *argv[])
{
    char *p_data;
    func1(p_data);
    //...
    return 0;
}
```

上代码用来简单描述任务需求，目是说明一定需要在函数内部调用malloc分配内存空间，但像上面那样直接分配就会导致函数func1结束后，指针p被释放掉（malloc分配的空间并没有被释放）所以，就找不到之前malloc分配的堆空间。

#### 示例1：一维

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void func1(char *p)
{
    p = (char *)malloc(sizeof(char)*10);
}

char* func2(char *p)
{
    p = (char *)malloc(sizeof(char)*10);
    return p;
}

char* func3(void)   //warning: function returns address of local variable
{
    char p[] = "three";
    return p;
} 

void func4(char **p)
{
    *p = (char *)malloc(sizeof(char)*10);
}

int main(int argc, char *argv[])
{
    //1.【错误】访问空指针导致越界，因为func1结束后p被释放，malloc分配的空间没有释放
    // char *p_data1;
    // func1(p_data1);
    // strcpy(p_data1, "one");  //Segmentation fault (core dumped)
    // printf(p_data1);

    //2.【正确】通过return返回指针，用来指向malloc分配的内存空间
    char *p_data2;
    p_data2 = func2(p_data2);
    strcpy(p_data2, "two");
    printf("%s\n",p_data2);

    //3.【错误】func3结束后p空间被释放，相对于func2，因为malloc分配的空间没有free是不会释放掉的
    //   所以func2返回指向的是malloc空间，而func3返回后访问的是野指针
    // char *p_data3;
    // p_data3 = func3();
    // printf("%s\n",p_data3);  //Segmentation fault (core dumped)

    //4.【正确】func4传入的是 &p_data4 它指的是传入的是指针p_data4所位于的内存地址
	//   而相对于func2传入的是 p_data2 它指的是传入的是指针p_data2所指向的内存空间
	//   这样说可能还有点蒙，举个例子某高速路上【杭州路段】有块【标识牌】，上面写着
	//  【距离上海还有XXXkm】,这个路牌它所在地址是【杭州】，但是它指向的是【上海】
	//   相对于p_data4就类于【杭州】，p_data2类于【上海】
    char *p_data4;
    func4(&p_data4);
    strcpy(p_data4, "four");
    printf("%s\n",p_data4);

    return 0;
}
```

相对于func4而言，func2就显得有些臃肿，不过效果都能同样实现，输出结果：

```shell
two
four
```

#### 示例2：二维

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

void func5(char ***p, int *len)
{
    char aa[] = "wersdfsdfsd";
    char bb[] = "jgj645675476345634563456345";
    char cc[] = "123";

    (*p) = (char **)malloc(3*sizeof(char));

    (*p)[0] = (char *)malloc(strlen(aa)*sizeof(char));
    (*p)[1] = (char *)malloc(strlen(bb)*sizeof(char));
    (*p)[2] = (char *)malloc(strlen(cc)*sizeof(char));

    strcpy((*p)[0], aa);
    strcpy((*p)[1], bb);
    strcpy((*p)[2], cc);

    *len = 3;
}

int main()
{
    int i, length;
    char **str;
    func5(&str, &length);
    for (i = 0; i < length; i++)
    {
        printf("-%02d- %s\n", i, str[i]);
    }
    return 0;
}
```

输出结果：

```shell
-00- wersdfsdfsd
-01- jgj645675476345634563456345
-02- 123
```

#### 拓展：

- ##### [栈和堆的区别](https://blog.csdn.net/u012836896/article/details/89973820)

根据申请方式不同，栈（stack）由系统自动分配，堆（heap）需要程序员自己申请。 

```c
char str1[20];							//栈
char *str2;
str2 = (char *)malloc(sizeof(char)*10);	//堆
```

malloc分配的空间在进程结束之前不会主动释放，需要手动释放执行free

```c
char *str;
str = (char *)malloc(sizeof(char)*10);
free(str);
```