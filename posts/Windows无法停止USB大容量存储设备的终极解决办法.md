# Windows无法停止USB大容量存储设备的终极解决办法

虽然现在的USB存储设备大多已经支持了热拔技术，但是总觉得通过利用Windows状态栏的「安全删除硬件并弹出媒体」功能弹出USB设备是最稳妥的方法。然而经过Win10多次更新以后，不知道为什么这个功能在我的电脑上不太好使了，经常遇到无法停止的问题，抛出一个烦人的提示「Windows无法停止设备『USB大容量存储设备』」。

![弹出USB大容量存储设备时出问题](https://ws2.sinaimg.cn/large/6273c3b7ly1ffrryta9c9j20h205yaa3.jpg)

可惜的是Windows并不会在提示里明确指出到底是哪些程序正在占用你的USB设备，往往等你把所有能想到的程序都关闭以后仍然无法安全弹出。

网上搜寻来的解决方法无外乎重启资源管理器、利用第三方软件「unlock」或「USB Safely Remove」、到资源监视器查找正在占用USB的进程等，然而这些传统的方法似乎在Win10上很难见效。重启资源管理器（explorer.exe）对于曾经的XP系统来说的确是行之有效的一种手段，而在如今大行其道的Win10系统上却几乎不会奏效。如果尝试通过各种手段查找占用USB设备的进程进而手动终止，往往会发现这样占用你的USB设备的进程居然是system，最终又回到了原点。

通过大量的摸索和搜寻，我终于找到了解决这一难题的简单有效的办法。

## 弹出U盘

对于U盘，通常按以下步骤操作即可安全弹出：

> 此电脑 - 右键U盘 - 弹出

如果还是弹出失败，请参考下面移动硬盘的方法。

## 弹出移动硬盘

由于移动硬盘并没有像U盘一样的弹出功能，稍微需要曲线救国一点。下面列举四种较为有效的方法：

###中止占用进程

通过「事件日志」查看具体是什么进程在占用移动硬盘：

> 打开控制面板 - 系统和安全 - 查看事件日志 - 展开左侧「Windows日志」下的「系统」- 在中间窗口找到最新的来源是Kernel-PnP的「警告」- 点开查看进程占用信息

如果占用进程是用户进程，则手动中止该进程，之后应该可以正常弹出设备；如果占用进程是system或其他不可手动中止的进程（如杀毒或防火墙等安全软件相关进程），则参照其他方法解决。

###卸载盘符

通过「磁盘管理」卸载移动磁盘的盘符

> 打开磁盘管理 - 右键移动硬盘项 - 更改驱动器号和路径 - 选中移动硬盘盘符 - 删除 - 确定

再尝试通过「安全删除硬件并弹出媒体」弹出设备，若仍然提示被占用，则参照其他方法解决。 

###强制下线硬盘（99%情况有效）

通过「磁盘管理」或命令行工具使移动硬盘转为「脱机」状态：

> 打开命令提示符cmd - 输入「diskpart」- 输入「list disk」，在出现的列表中找到移动磁盘的编号 - 输入「select disk x」（x为移动磁盘编号）- 输入「offline disk」

完成以上步骤后，移动硬盘被强制转为「脱机」状态，此时再尝试通过「安全删除硬件并弹出媒体」弹出设备。

为使移动硬盘在下次接入时能够正确挂载，建议再多输入一条`online disk`命令，使硬盘「上线」。

以下是「superuser」社区@[Diego Queiroz](https://superuser.com/users/66101/diego-queiroz)网友根据此方法提供的自动化`.bat`脚本：

```powershell
@echo off
@cls

set tempfile="%TEMP%\diskscrp.dsk"

echo.
echo   === Disk removal tool ===
echo.
echo   Select the disk volume number
echo   (if the disk has multiple volumes, select any of them)
echo.
echo list volume | diskpart | findstr /C:Volume /C:---
echo.
set /p volume="   Selected volume: "
echo.

echo select volume %volume% >>%tempfile%
echo offline disk >>%tempfile%
echo online disk >>%tempfile%

diskpart /s %tempfile% | findstr /C:"not valid"

if "%ERRORLEVEL%"=="1" (
  echo   Disk has been unlocked successfully.  Try to safely remove it now.
)

del /F %tempfile%
```

##最后的方法（安全）

如果以上方法都不奏效，请尝试在关机状态下拔出 USB 线。

## Tips

通过微软官方提供的「[Process Explorer](https://docs.microsoft.com/zh-cn/sysinternals/downloads/process-explorer)」GUI和「[Handle](https://docs.microsoft.com/zh-cn/sysinternals/downloads/handle)」命令行小工具，可以查看占用进程（process），并分析占用线程（handle），从而有针对性的手动结束对USB设备上的文件占用。

具体操作方法参照：[How to unlock a file handle locked by SYSTEM or any other active process in Windows](https://www.ryadel.com/en/unlock-file-handle-locked-system-active-process-windows/)

Handle主要命令（以**管理员模式**运行）：

1. `handle64.exe > output.txt`：输出所有活跃进程 / 线程到output.txt文件（线程ID以其左侧16进制数表示，线程往上找到最近分隔线处为其对应的进程）。
2. `handle64.exe -c <handleID> -p <processID>`：关闭某一线程。
3. `handle64 X:\`：列出当前占用X盘的进程 / 线程。

## 题外话

我发现一个从Win7时代一直用到Win10时代、更换过硬盘盒的移动硬盘总是会出现占用问题，使用Handle查看占用进程 / 线程后发现移动硬盘中有一些奇怪的文件被占用，查看事件日志发现是cmdagent（Comodo防火墙的核心进程）和system阻止弹出。Google后发现可能为NTFS文件系统文件被锁定的缘故（计算机将移动硬盘识别成了固定硬盘？），疑为历史遗留问题，原装的移动硬盘似乎没有出现过这种现象。另外，至于为什么Comodo占用了移动硬盘，我尝试将Comodo所有防护手动关闭仍无法解决问题，推测可能是这些系统文件受到了Comodo安全规则的保护。

出现这种情况的移动硬盘可以通过「强制下线硬盘」的方法解除占用。

![Handle查看占用USB设备的进程和线程](https://ws4.sinaimg.cn/large/6273c3b7ly1fpkox7i4acj20v405hjrs.jpg)

目前尚未找到针对此问题的完美解决方法。（什么？你说格式化硬盘？不存在的！）

PS: 关于计算机的问题最好通过Google搜寻英文资料，解决问题远比参考中文资料来得高效。

## References

1. ["Windows无法停用设备，原因是某个程序正在使用它。"的解决办法](http://blog.sina.com.cn/s/blog_60a760f80101a20x.html)
2. [How to unlock a file handle locked by SYSTEM or any other active process in Windows](https://www.ryadel.com/en/unlock-file-handle-locked-system-active-process-windows/)
3. [Windows System Process locking External HDD](https://answers.microsoft.com/en-us/windows/forum/windows_7-hardware/windows-system-process-locking-external-hdd/76170b41-c5e5-4cad-a20e-793d2a8b2b16)
4. [External HDD is always in use when trying to safely remove](https://superuser.com/questions/510968/external-hdd-is-always-in-use-when-trying-to-safely-remove)
5. [Safely remove external USB drive fails due to $extend](https://superuser.com/questions/420168/safely-remove-external-usb-drive-fails-due-to-extend)