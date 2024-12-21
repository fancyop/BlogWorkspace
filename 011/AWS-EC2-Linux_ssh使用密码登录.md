# AWS EC2 Linux | ssh 使用密码登录

### 1、使用AWS控制台创建的密钥对或者直接通过网页登录

###### 注意：使用pem私钥不允许直接登录root用户，只能登录默认用户例如：`ec2-user` ,`ubuntu`等等

### 2、创建root密码

```bash
sudo passwd root
```

### 3、切换到root用户

```bash
su root
```

### 4、修改 `sshd_config` 文件

```bash
vim /etc/ssh/sshd_config
```

- 允许使用密码登录

  ```
  PasswordAuthentication yes
  ```

- 允许root用户登录

  ```
	PermitRootLogin yes
  ```

######   注意：如果 `PermitRootLogin` 项找不到自行添加

### 5、重新启动ssh服务

```bash
systemctl restart sshd
```

------

### 至此大功告成，可以使用ssh直接通过密码登录



*参考：*

*http://www.unclealan.cn/index.php/system/176.html*

*https://nachifur.blog.csdn.net/article/details/105399039*

