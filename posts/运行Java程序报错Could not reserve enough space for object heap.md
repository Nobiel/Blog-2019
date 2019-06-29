# 运行Java程序报错 "Could not reserve enough space for object heap"

今天使用`java -Xmx1024m`运行编译好的Java程序是出现错误：

“Could not reserve enough space for object heap”

即分配给heap空间不足。

查阅资料后发现32位的Java限制了最大可分配内存，重新下载安装64位Java后问题解决。

[64位Java下载地址](https://www.java.com/zh_CN/download/manual.jsp)