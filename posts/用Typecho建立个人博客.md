# 用Typecho建立个人博客

## 建立初衷

每次遇到计算机方面的问题，用百度或者Google检索解决方案时，总能找到很多的个人独立博客，而自己遇到的问题及解决办法很多时候都早已记录在这些个人博客之中。不得不说Google搜寻IT问题的能力真是相当的强，无效和重复信息较少，大部分信息质量较高，尤其是英文信息（百度与之相比在英文搜寻能力方面实在是相当逊色）。看着现在很多人都建立起了属于自己的个人独立博客，心想以文字的形式总结记录在学习中遇到的问题和解决问题的过程，其实在帮助他人的同时也提升了自己的总结归纳能力，并且日后如果在遇到相同的问题时可以随时回过头来查阅 ，何乐而不为呢？

自己手里刚好有一台美国C3机房的VPS，号称电信、移动、联通三网直连，不管是用来科学上网还是建站都是不错的选择（想入手的可以走我的[推荐链接](https://manage.hostdare.com/aff.php?aff=346)），于是便拿来建立个人博客了。

## 博客程序的选择

现今用来建立个人博客的最火程序莫过于大名鼎鼎的Wordpress了，与之同类的还有Typecho、Hexo、Ghost等等。考虑到Wordpress过于臃肿的功能和其他一些因素，最终选择了国人开发的Typecho作为建立博客的程序。

为什么选择Typecho？因为它轻量高效、先进稳定、简洁友好，并且原生支持Markdown排版语法，这就足够了。建立博客并不需要太多的功能，只需使我专注于写作而不是费心思去鼓捣程序，前期了解过后，我决定选择Typecho。

**2017.11.23 更新：注意Typecho现已更新至1.1正式版，以下搭建过程以1.0为例。**

## VPS环境

- CentOS 6 x86_64
- OpenVZ
- 512MB RAM（安装LNMP一键包需要128MB以上内存）
- 10GB SSD（安装LNMP一键包需要5GB以上硬盘剩余空间）

## 安装LNMP

### 搭建LNMP环境

由于之前已经多次安装过LNMP环境，对我来说这一步已经比较熟悉了。

选择[LNMP一键安装包](https://lnmp.org/)进行搭建，安装方法见[这里](https://lnmp.org/install.html)，不再赘述。

安装过程中需要注意的是Typecho对安装环境的要求（LNMP v1.3默认满足要求）：

> - PHP 5.2 以上
> - MySQL, PostgreSQL, SQLite 任意一种数据库支持，并在 PHP 中安装了相关扩展
> - CURL 扩展支持
> - mbstring 或 iconv 扩展支持

**一定要记得安装时输入的MySQL数据库root密码！**

### 添加网站（虚拟主机）

搭建好LNMP环境之后需要添加一个虚拟主机，作为博客站点。详细添加方法[见此](https://lnmp.org/faq/lnmp-vhost-add-howto.html)，记得将申请好的域名做好解析记录（本站选择免费[tk域名](http://www.dot.tk)及[CloudXNS](www.cloudxns.net)解析）。

LNMP一键包自带Typecho伪静态规则，添加网站时可以选择开启伪静态（之后也需要进入博客后台开启伪静态设置），有利于网站收录。

**这里的MySQL user密码不同于前面安装LNMP时输入的MySQL root密码，是Typecho数据库的密码，同样需要记住。**

## 安装Typecho

选择[Typecho 1.0 (14.10.10)](http://typecho.org/download)正式稳定版。 

```bash
#下载程序
wget https://github.com/typecho/typecho/releases/download/v1.0-14.10.10-release/1.0.14.10.10.-release.tar.gz
#解压缩到博客站点目录（注意替换为自己的路径）
tar -xzf 1.0.14.10.10.-release.tar.gz -C /home/wwwroot/blog
cd /home/wwwroot/blog
mv build/* . && rm -rf build
```

此时`/home/wwwroot/blog`下的目录结构应该是这样的：

```
/admin/
/install/
/usr/
/var/
/license.txt  
/index.php
/install.php
```

此时只需访问博客的域名地址即可开始Typecho的安装工作，安装过程非常简单，参见[Typecho官方文档](http://docs.typecho.org/install)。

## 开启全站HTTPS

开启HTTPS有很大的好处，一是为了传输安全，二是为了更好地被搜索引擎收录。

[这里](https://www.bakumon.me/p/typecho-ssl-https.html)有一篇不错的使用Lets's Encrypt免费SSL证书开启HTTPS的教程，可以参考。

成功启用HTTPS后，需要注意几个问题：

1. 登录Typecho后台 - 设置 - 基本设置 - 站点地址，将http域名改成https域名。

2. 编辑Typecho站点根目录下的文件`config.inc.php`加入下面一行配置，否则网站后台还是会调用HTTP资源。

   ```php
    /** 开启HTTPS */
   define('__TYPECHO_SECURE__',true);
   ```

   某些浏览器对HTTPS要求较高，如果出现警告提示，可以按F12查看是否仍然加载了HTTP资源。

   比如评论表单的action地址还是HTTP，找到站点主题目录下的`comments.php`文件，将`$this->commentUrl()`替换为`echo str_replace("http","https",$this->commentUrl());` ，保存。

3. 开启HTTPS之前上传的附件和图片，其链接会锁死为HTTP，需要替换为HTTPS开头。

   进入typecho数据库，使用命令批量替换`typecho_contents`表的`test`字段：

   ```sql
   UPDATE `typecho_contents` SET `text`=REPLACE(`text`,'http','https')
   ```


## 404错误解决方法

**LNMP v1.4**环境下，如果未正确配置pathinfo，Typecho站点部分页面将会出现404错误。

解决办法参照[LNMP添加、删除虚拟主机及伪静态使用教程](https://lnmp.org/faq/lnmp-vhost-add-howto.html)的「pathinfo设置」说明。

针对LNMP v1.4：

> 修改对应虚拟主机的配置文件(/usr/local/nginx/conf/vhost/域名.conf)
> 将```include enable-php.conf;```替换为```include enable-php-pathinfo.conf;```
>
> 修改pathinfo需要重启nginx生效。

## References

1. [Typecho文档站点](http://docs.typecho.org/doku.php)
2. [Typecho全站启用HTTPS教程](https://faq.xiaoz.me/archives/64.html)
3. [typecho开启https](https://zlfzy.com/note/6.html)
4. [为 typecho 开启小绿锁](https://www.bakumon.me/p/typecho-ssl-https.html)
5. [LNMP添加、删除虚拟主机及伪静态使用教程](https://lnmp.org/faq/lnmp-vhost-add-howto.html)