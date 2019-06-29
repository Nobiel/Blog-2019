#Win10卸载双系统后恢复单系统引导的方法

折腾过Win10双系统后卸载另一个系统，可能会出现另一个系统的开机引导未完全删除的情况，下面以两个典型例子说明解决方法。

## Win10 / Linux

如果Linux没有卸载完全，可能会导致grub引导残留。症状为开机后先出现黑色的grub命令行界面，输入「exit」命令退出引导，然后可以正常进入Win10系统。

解决方法：在Win10系统下以**管理员模式**运行命令提示符或者PowerShell，输入「bcdboot X:\Windows」命令恢复原Win10系统引导（X为Win10系统所在盘符）。

*这一步可能不是必须的：在计算机的BIOS设置中删除开机Boot中的Linux系统。*

## Win10 / Win 8.1

如果Win8.1没有卸载完全，可能会导致Win8.1引导残留。症状为进入系统前出现蓝色的Win10 / Win8.1系统选择界面，选择Win10后可以正常进入Win10系统。

解决方法：Win+ R运行「msconfig」，打开系统配置，选择「引导」页，删除Windows 8.1的引导后点「应用」-「确定」。

## Reference

https://social.technet.microsoft.com/Forums/zh-CN/6b18ba0f-fcc1-4abf-8171-e47f23cd2bfa/windows10-linuxlinuxgrubwindows?forum=win10itprogeneralCN