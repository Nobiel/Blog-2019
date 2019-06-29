# TeXstudio提示找不到TeX Live的解决办法

刚在Win10下装完Tex Live（安装到D盘），打开TeXstudio却提示找不到Tex Live。打开cmd输入「tex --version」命令，提示「『tex』不是内部或外部命令，也不是可运行的程序或批处理文件」，百度一下发现是Windows的「PATH」环境变量没有设置好。

**解决办法**

右击「此电脑」- 「高级系统设置」- 「环境变量...」，编辑「用户变量」或「系统变量」下的「PATH」变量，将TeX Live的bin目录「D:\texlive\2017\bin\win32」加入其中，重启TeXstudio即可。