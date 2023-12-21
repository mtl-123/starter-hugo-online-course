---
title: 🤖 Windows系统命令
linkTitle: Windows系统
summary: 系统
date: "2023-06-05"
type: book
tags:
  -
---

{{< toc hide_on="x1" >}}

[无法在驱动器0的分区1上安装windows](https://cloud.tencent.com/developer/article/2291476)

[dupeGuru](https://dupeguru.voltaicideas.net/)

[czkawka](https://github.com/qarmin/czkawka/releases)

[Windows 系统信息查询命令](https://pingmaoer.github.io/2020/03/30/%E5%86%85%E7%BD%91%E4%BF%A1%E6%81%AF%E6%94%B6%E9%9B%86%E4%B8%80/)

[windows 系统修改用户密码](https://support.microsoft.com/zh-cn/windows/%E6%9B%B4%E6%94%B9%E6%88%96%E9%87%8D%E7%BD%AE-windows-%E5%AF%86%E7%A0%81-8271d17c-9f9e-443f-835a-8318c8f68b9c)

[不错的看图软件picview](https://picview.org/download)

[windows共享文件终端操作命令](https://cloud.tencent.com/developer/article/2155969)

[windows系统打开应用命令大全](https://blog.csdn.net/wdxrzYang/article/details/113063597)

[远程桌面工具大全](https://en.lo4d.com/windows/remote-desktop)

[windows命令的 winget 用法  ](https://juejin.cn/post/6976120541518233607)

[安装 scoop](https://scoop.sh/)

[wmic命令的用法](https://www.yumefx.com/?p=1137)

## 路由跟踪

`tracert  ip或者域名`

## 解析域名

`nslookup`

## 任务计划
`taskschd.msc`

## Windows 常用命令

[命令](https://www.hdd-tool.com/zh-TW/windows-11/disk-cleanup-on-c-drive.html)

## 一、清除 C 盘垃圾

```bat
1、按Windows + R 一起在键盘上

2、输入 " cleanmgr " 并按回车，默认选择C盘，直接点击OK以继续
```

## 二、手动清除所有垃圾

```bat
1、按Windows + R 一起在键盘上

2、输入 "  cleanmgr /LOWDISK " 然后按Enter
```

## 三、自动清理所有垃圾文件

```bat
1、 安装Windows + R 一起在键盘上

2、 输入 "leanmgr /VERYLOWDISK" 然后按Enter
```
## 四、打印机

```bat
# 打印机设置
1、 "control printers"
```

## 五、远程桌面

```bat
# 打开远程桌面设置
1、"systempropertiesremote"

# 打开远程桌面
2、"mstsc"
# 打开控制面板
`control panel`
# 使用命令连接远程桌面
3、"mstsc /v:192.168.1.89"
```

## 六、防火墙
```bash
# 打开防火墙
1、"control firewall.cpl"
```


## 七、打开网络设置

```bat
# 打开网络设置
1、"ncpa.cpl"
```

## 打开本地安全策略
```bash
secpol.msc
```

## 更新策略
```bash
gpupdate /force
```

## 查看凭证
`net usr`
## 清除用户凭据
```bat
# 删除所有网络驱动凭证
net use * /del /y

# 查看网路驱动
net view \\10.0.0.19

# 映射到本地驱动网盘
net use Z: \\10.0.0.19\共享文件

# 删除本地网络盘符
net use Z: /del
```
## 删除所有的网络盘符


## 打开凭证管理器
`control keymgr.dll `


## 打开本地策略编辑器

`gpedit.msc`

## 打开服务

`services.msc`

## 用户账户

`rundll32 netplwiz.dll,UsersRunDll`

## 打开控制面板

`control panel`

## 打开计算机

`calc`

## 配置环境变量后立马生效
`win+r  cmd  在终端输入  echo %path%`即可生效


## 强制执行本地组策略，重启服务器，整个配置过程完成。
`gpupdate /force`

## 程序功能
` appwiz.cpl`



## windows 查看指定日志 30行
`Get-Content  'C:\zabbiix Agent 2\zabbix_agent2.log' -Wait -Tail 30`


## 打开windows 日志查看启
`eventvwr`

## 系统配置
`msconfig`

## 打开磁盘管理
`diskmgmt.msc`


## 查看某个进程
```bat
C:\Users\MTL>tasklist | findstr zabbix
zabbix_agent2.exe            13716 Services                   0     12,104 K
```

![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/b999fe57-796b-4adf-b31a-9194801763e6)



![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/78842d07-e841-4554-91cc-9ee8d8da7886)


![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/d1a1393f-4620-416f-b39e-926d1aea929a)

## 脚本重启文件资源管理器

vim 重启资源管理器.bat

```bat
taskkill /f /im explorer.exe & start explorer.exe
```


## Windows 10 永久禁用自动更新 也用于 Windows Server 2019
```bat
chcp 65001
@echo off
echo 正在停止 Windows 更新服务...
net stop wuauserv

echo 禁用 Windows 更新服务...
sc config wuauserv start=disabled

echo 禁用自动更新策略...
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f

echo 禁用自动更新计划任务...
schtasks /Change /TN "Microsoft\Windows\WindowsUpdate\Automatic App Update" /Disable

echo 禁用自动维护计划任务...
schtasks /Change /TN "Microsoft\Windows\TaskScheduler\Maintenance Configurator" /Disable

echo 已成功禁用自动更新。
pause

```

## Windows 11 永久禁用自动更新

```bat
chcp 65001

@echo off
echo 正在停止 Windows 更新服务...
net stop wuauserv

echo 禁用 Windows 更新服务...
sc config wuauserv start=disabled

echo 禁用自动更新策略...
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f

echo 已成功禁用自动更新。
pause

```

## 脚本

```bat

@echo off
echo 正在停用不必要的服务…
sc config "alg" start=disabled
sc config "ClipSVC" start=disabled
sc config "DiagTrack" start=disabled
sc config "dmwappushservice" start=disabled
sc config "FDResPub" start=disabled
sc config "lfsvc" start=disabled
sc config "MapsBroker" start=disabled
sc config "MessagingService" start=disabled
sc config "PimIndexMaintenanceSvc" start=disabled
sc config "RetailDemo" start=disabled
sc config "RemoteRegistry" start=disabled
sc config "SensorDataService" start=disabled
sc config "SensorService" start=disabled
sc config "smphost" start=disabled
sc config "Spooler" start=disabled
sc config "tiledatamodelsvc" start=disabled
sc config "tzautoupdate" start=disabled
sc config "WalletService" start=disabled
sc config "WbioSrvc" start=disabled
sc config "WMPNetworkSvc" start=disabled
sc config "wscsvc" start=disabled
sc config "XboxNetApiSvc" start=disabled
echo 不必要的服务已停用
echo 正在禁用启动项…
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "DisabledHotkeys" /d "SG" /t REG_SZ /f > nul
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" /v "HideIcons" /d "1" /t REG_DWORD /f > nul
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoCDBurning" /d "1" /t REG_DWORD /f > nul
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoLowDiskSpaceChecks" /d "1" /t REG_DWORD /f > nul
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoRecentDocsHistory" /d "1" /t REG_DWORD /f > nul
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NlaSvc\Parameters\Internet" /v "EnableActiveProbing" /d "0" /t REG_DWORD /f > nul
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Dfrg\BootOptimizeFunction" /v "Enable" /d "Y" /t REG_SZ /f > nul
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" /v "NoRemoteRecursiveEvents" /d "1" /t REG_DWORD /f > nul
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "DisableLastAccess" /d "1" /t REG_DWORD /f > nul
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "EnableLUA" /d "0" /t REG_DWORD /f > nul
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" /v "VerboseStatus" /d "1" /t REG_DWORD /f > nul
echo 启动项已禁用
echo 正在清理磁盘空间…
cleanmgr /autoclean /d %SystemDrive% > nul
echo 磁盘空间已清理
echo 正在更新驱动程序和系统补丁…
start ms-settings:windowsupdate-action > nul
echo 驱动程序和系统补丁已更新
echo 正在禁用不必要的开机启动程序…
wmic startup where "Caption!=''" call disable > nul
echo 开机启动程序已禁用
echo 正在进行磁盘碎片整理…
defrag /C /H /U /V >nul
echo 磁盘碎片整理已完成
echo Windows 11 开机启动加速已完成

```
## 脚本二 

```bat
@echo off
Title Windows 11 Optimization Script
REM 启用延迟扩展模式
SETLOCAL ENABLEDELAYEDEXPANSION
REM 禁用自动更新
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v "NoAutoUpdate" /t REG_DWORD /d "1" /f

REM 缩短Windows启动时间
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management\PrefetchParameters" /v "EnablePrefetcher" /t REG_DWORD /d "1" /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management\PrefetchParameters" /v "EnableSuperFetch" /t REG_DWORD /d "1" /f

REM 禁用Windows搜索相关服务
PowerShell.exe -Command "Get-Service Wsearch | Set-Service -StartupType Disabled"

REM 关闭Windows Defender
PowerShell.exe -Command "Set-MpPreference -DisableRealtimeMonitoring $true"

REM 关闭系统恢复功能
PowerShell.exe -Command "Disable-ComputerRestore -Drive 'C:\'"

REM 禁用超级预读取
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management\PrefetchParameters" /v "EnableSuperfetch" /t REG_DWORD /d "0" /f

REM 清理系统垃圾文件
cleanmgr /sageset:65535 & cleanmgr /sagerun:65535

REM 禁用Windows默认磁盘碎片整理程序
PowerShell.exe -Command "Disable-ScheduledTask -TaskName 'ScheduleDefrag'"

REM 调整默认视觉效果
reg add "HKCU\Control Panel\Desktop\WindowMetrics" /v "MinAnimate" /t REG_SZ /d "0" /f

REM 禁用重复的启动项
PowerShell.exe -Command "Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Group-Object | Where-Object Count -gt 1 | ForEach-Object { $_.Group | Select-Object -Skip 1 } | Remove-ItemProperty"

REM 打开Power Plan在高性能模式下
PowerCfg /SETACTIVE SCHEME_MIN

REM 设置PowerShell执行策略
Set-ExecutionPolicy Unrestricted -Scope CurrentUser

REM 结束脚本
exit /b
```
## 脚本三
```bat
@echo off
set /p delay=请输入等待时间（秒）：
echo 正在关闭计算机，等待 %delay% 秒…
shutdown /s /t %delay%
echo 计算机已关闭
pause
```

## 脚本四
以下是PowerShell脚本的完整代码，用于安装远程桌面服务角色和远程桌面服务管理器（Remote Desktop Services Manager）：

```powershell
Import-Module ServerManager
Add-WindowsFeature RDS-RD-Server
Add-WindowsFeature RSAT-RDS-Tools

```

使用方法：

打开任意文本编辑器，将上述代码复制到文本编辑器中，并另存为Install-RDSScript.ps1。

打开PowerShell控制台，浏览到Install-RDSScript.ps1所在的目录。

# 脚本五
运行以下命令以启用PowerShell脚本的执行权限：

```powershell
Set-ExecutionPolicy Unrestricted
```
运行以下命令以执行PowerShell脚本：
# 脚本六
```powershell
.\Install-RDSScript.ps1
```

执行完成后，可以在“远程桌面服务管理器”中找到远程桌面服务管理器（Remote Desktop Services Manager），并打开以使用。
希望这个PowerShell脚本代码能够帮助您快速、方便地安装"远程桌面服务管理器"在Windows服务器上，进行批量管理Windows远程桌面机器。

## 如何批量管理远程主机方法

### 方法一： 创建文件后缀为.rdp的方式

1. 创建一个总目录例如： RDP_remove_manager_host
2. 创建一个文件修改为例如我要远程10.0.0.3主机 就是10.0.0.3.rdp 
3. 鼠标右击选择编辑，并填入10.0.0.3 和用户名密码保存即可

![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/466067f1-4522-4103-b3ce-bb840c0e3d4c)

![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/2ab530a2-65f1-43b2-bd07-949d51f1ed5a)

## 统计TCP总连接个数

需打开powershell

`(Get-NetTCPConnection | Measure-Object).Count`
