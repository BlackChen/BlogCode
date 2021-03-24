MySQL

# MySQL安装
## rpm安装
## yum安装
## 压缩包安装
安装包：https://cdn.mysql.com/archives/mysql-5.6/mysql-5.6.51-linux-glibc2.12-x86_64.tar.gz

# MySQL卸载
```
//rpm包安装方式卸载
查包名：rpm -qa|grep -i mysql
删除命令：rpm -e –nodeps 包名
 
//yum安装方式下载
1.查看已安装的mysql
命令：rpm -qa | grep -i mysql
2.卸载mysql
命令：yum remove mysql-community-server-5.6.36-2.el7.x86_64
查看mysql的其它依赖：rpm -qa | grep -i mysql
 
//卸载依赖
yum remove mysql-libs
yum remove mysql-server
yum remove perl-DBD-MySQL
yum remove mysql
————————————————
版权声明：本文为CSDN博主「回归（10166005）」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_41829904/article/details/92966943
```

# MySQL常见错误

## 安装
- [Found option without preceding group in config file /etc/my.cnf at line 1!](https://www.cnblogs.com/gxs-future/p/13600990.html)

- *error while loading shared libraries: libaio.so.1*

场景：
在CentOS7.6系统下安装Mysql5.7时，初始化的时候报错：
```
mysqld: error while loading shared libraries: libaio.so.1:cannot open shared object file: No such file or directory
```
解决：
因为是新的系统，并没有安装所需要的依赖包
```
yum install -y libaio
```

然后重新初始化即可。
