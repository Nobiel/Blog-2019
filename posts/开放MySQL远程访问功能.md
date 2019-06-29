# 开放MySQL远程访问功能

搭建后LNMP环境后MySQL数据库的远程访问功能默认是关闭的，用到时需要开启。

## 防火墙开放3306端口

CentOS6下：

查看防火墙规则：`iptables -L -n ` 

发现3306端口规则为DROP，需要手动开放端口：

```bash
#删除DROP规则（3306端口规则位于INPUT链第5行）
iptables -D INPUT 5
#添加ACCEPT规则
iptables -A INPUT -p tcp -m tcp --dport 3306 -j ACCEPT
```

保存规则：`service iptables save`

重启防火墙生效：`service iptables restart`

*注：不建议直接修改 /etc/sysconfig/iptables*

关于iptables的规则配置，可以参考[这里](http://www.111cn.net/sys/linux/53012.htm)。

## MySQL开放远程访问权限

登录数据库：`mysql -u root -p`

创建远程登录用户并授权：

```bash
grant select,lock tables on demodb.* to demouser@'%' identified by 'your password';
```

刷新权限以生效：`flush privileges;`

解释：demodb=数据库名；demouser=数据库用户名；your password=数据库密码

上面的语句表示将 demodb 数据库的select，locked权限授权给demouser这个用户，允许 demouser用户在任何地点进行远程登陆，并设置 demouser用户的密码为your password。

demodb.* 表示上面的权限是针对于哪个表的，demodb 是某个数据库，后面的 * 表示对于所有的表，由此可以推理出：对于全部数据库的全部表授权为“.”，对于某一数据库的全部表授权为“数据库名.*”，对于某一数据库的某一表授权为“数据库名.表名”。

“%”表示不限制链接的 IP ，如果想限制IP则设置“%”为允许远程连接的 IP 地址即可。

最后可以查看一下用户表里的内容，开启MySQL远程登录的用户都存储在MySQL数据库中的user表中。

```bash
#选择mysql数据库
use mysql;
#查看用户表
select host,user from user;
```

关于MySQL的常用命令，可以参考[这里](http://218.194.248.2/~wuxiaogang/cpcourse/database/mysql/index.htm)。

## Reference

[MySQL数据库如何开启远程连接](http://blog.dbfen.com/?p=380)