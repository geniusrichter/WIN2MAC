# WIN2MAC
WIN10、黑苹果一键重启切换系统

单硬盘安装WIN10和黑苹果远程主机，发现无法通过远程重启来切换系统。本项目通过批处理来实现该功能。

思路：将config.plist改成指定启动MAC系统，放到WIN10下；将config.plist改成指定启动WIN10系统，放到MAC下。通过批处理实现：挂载EFI分区-复制配置文件到EFI分区-重启。
-
MAC下重启到WIN10配置文件：
-
修改config.plist以下字段：

搜索`<key>DefaultLoader</key>`

修改内容为`<string>\EFI\Microsoft\Boot\bootmgfw.efi</string>`需根据实际情况修改。

搜索`<key>DefaultVolume</key>`

修改内容为`<string>EFI</string>`需根据实际情况修改。

改完后放到MAC文档下

新建文本文档，并更名为MAC2WIN.command

打开终端，输入chmod +x MAC2WIN.command，给批处理赋予执行权限。

用文本编辑器将以下代码复制进MAC2WIN.command并保存。

```
sudo diskutil mount disk0s1
cp -R /Users/*****/Documents/config.plist /Volumes/*****/EFI/CLOVER
sudo shutdown -r now
osascript -e 'tell application"Terminal" to close (every window whose name contains".command")' &
exit
```

我的EFI分区为disk0s1，需根据自己情况替换。*****也要更具实际情况替换，至此MAC2WIN搞定！
-

WIN10下重启到MAC配置文件：
-
修改config.plist以下字段：

搜索`<key>DefaultLoader</key>`

修改内容为`<string>boot.efi</string>`需根据实际情况修改。

搜索`<key>DefaultVolume</key>`

修改内容为`<string>MacOS</string>`需根据实际情况修改。

改完后放到C盘reboot2mac文件夹下

新建文本文档，并更名为WIN2MAC.bat

用文本编辑器将以下代码复制进WIN2MAC.bat并保存。

```
echo list disk|%windir%\system32\diskpart.exe
cmd /c "echo select disk 0 & echo select partition 1 & echo assign letter=m & echo list volum & echo exit"|%windir%\system32\diskpart.exe

copy c:\reboot2mac\config.plist m:\efi\clover\

shutdown -r -t 0
```

我的EFI分区为disk 0,partition 1,映射盘符为M，需根据自己情况替换。
-
最后一步将WIN2MAC.bat创建快捷方式到桌面，右键-属性-快捷方式-高级-勾选“用管理员身份运行”。至此WIN2MAC搞定！
-
