# AK-SP3000-Root-Guide
# 艾利和SP3000的Root指北

Rooting the Astell&amp;Kern SP3000 with Apatch  
通过Apatch来获取SP3000的Root权限

## [Chinese Version](https://github.com/PPPPatrick0/AK-SP3000-Root-Guide/blob/main/README.md#%E4%B8%AD%E6%96%87%E7%89%88chinese-version)
## [English Version](https://github.com/PPPPatrick0/AK-SP3000-Root-Guide/blob/main/README.md#english-version-1)

## 中文版（Chinese Version）

### 简短说明

* 本指南旨在为艾利和 (Astell&Kern) SP3000 播放器提供一套详尽且经过验证的Root方案。本方案基于 SP3000 （无印版）、系统版本 v1.51 。

* 理论上，类似机型如 SP3000T 等（甚至PD10和SP4000），也可参照本指南的核心思路进行尝试，但操作者需自行承担因设备或系统版本差异带来的未知风险。

### 重要提示

***风险警告:***
* Root过程可能将伴随着失去保修、数据丢失甚至设备永久损坏（变砖）的风险。请您在充分理解所有步骤和潜在风险后再进行操作。所有操作均由您本人负责。

**环境要求:**
* 由于在Windows下，Google USB驱动程序存在兼容性问题，导致设备在特定模式（如fastbootd）下无法被主机识别（在SP3000上得到证实）。  
因此，本指南强烈建议并要求在Linux环境下完成所有刷写操作，以确保流程的顺利进行。

**APK包名确认:**
* 由于AK操作系统采用白名单制度来限制APK文件的安装，本指南中的所有APK文件均通过了修改Package Name的方式来绕过AK的限制。  
因此，每一个修改后的APK文件格式均为xxx_Modded(package_name).apk的格式上传，以方便你自行查找是否存在包名冲突的Application。

### 操作流程概览
**本指南将引导您完成以下三个核心步骤：**
* 准备工作: 在设备上启用开发者选项，并打开USB调试权限。
* 核心刷写: 使用fastboot工具，将预先准备好的定制化镜像刷入您的设备。
* 安装管理器: 安装APatch管理器 (KernelPatch)，用于后续的Root权限管理。

#### 1 准备工作
在设备上启用开发者选项，并打开USB调试权限。  
由于SP3000没有在常规设置中提供开启ADB调试的入口，我们需要通过一个终端命令来调用其隐藏的工厂调试菜单。

##### 1) 安装终端模拟器
在Release中，下载 APK.zip。  
定位到 Term_Modded(uk.co.sevendigital.android).apk 这个文件。这是一个经过修改的终端模拟器应用。  
将此APK文件传输到您的SP3000设备内部存储中，并进行安装。

##### 2) 获取完整的读写权限
Astell&Kern的系统存在一个关于文件权限的特殊Bug。为了确保终端应用能正常工作，请遵循以下步骤：  
· 首次安装后：启动一次刚刚安装的终端应用。如果系统提示请求文件读写权限，请务必“允许”。  
· 退出应用。  
· 再次安装：使用任意方式，找到并重新安装一次 Term_Modded 应用（覆盖安装即可）。  
（注：如果不知道怎么样再次安装的可以尝试使用同在APK.zip里的 Files_By_Google_Modded(deezer.android.app).apk ，  
请通过chrome开启localsend或类似局域网传输程序将apk文件下载到设备本地，然后同样进行两次安装，  
这样Files_By_Google才能获得正确的文件读写权限，否则进入Files_By_Google会什么都看不到）  
· 再次启动终端应用。如果系统再次提示请求权限，请同样“允许”。  

##### 3) 开启ADB调试
在终端模拟器应用中，输入以下命令，然后按回车键：  
```
am start --user 0 -n com.iriver.tester.factorytool/.DebugSettingActivity
```
执行后，您将直接进入Astell&Kern的工厂调试界面。  
在此界面中，找到 ADB Debug 选项，并将其开启。  

##### 4) 验证连接
现在，使用USB数据线将您的SP3000连接到Linux主机。  
在主机的终端里，输入以下命令：
```
adb devices
```
如果您看到输出了您设备的序列号，并且状态为 device，则表示ADB连接已成功建立。

##### 5) （可选，但强烈推荐）永久开启ADB调试
默认情况下，每次重启SP3000后，ADB调试选项都会自动关闭。  
为了方便后续操作及日后使用，您可以将其设置为永久开启。  
在主机的终端里，输入以下命令：
```
adb shell am start --user 0 -n com.iriver.tester.factorytool/.UserDebugActivity
```
设备的屏幕上会再次出现一个工厂调试界面。  
点击此界面上的第一个选项（与ADB相关的选项）。  
完成此操作后，ADB调试模式将会被永久激活，即使设备重启也不会关闭。

#### 2 镜像刷写
在这一步，我们将通过fastbootd模式，向设备刷入三个镜像文件，分别为 vendor.img 和 vbmeta.img 以及 vbmeta_system.img。  
***重要***：开始之前，请确保您已经从本指南的Release中下载了完整的配套文件（OTA_files.zip）。

##### 1) 刷写定制化的 vendor.img
我们首先需要刷入一个经过特殊修改的vendor分区镜像。  
* 定位文件：
找到路径为 OTA_files/img/patched/ 的子目录。  
我们将要使用的是此目录下的 vendor.img 文件。  
* 关于此文件的说明：
此vendor.img源自官方OTA升级包，但对其内部的一个关键文件 etc/selinux/precompiled_sepolicy 进行了一些必要（也许）的微调。  
适用性警告：目前提供的这个vendor.img仅在SP3000标准版（无印版）上测试通过。  
（免责声明/作者注）：如果怕我往里面投毒的话，我会在以后详细说明我是如何进行修改的（咕咕咕~），也便其他机型（如SP3000T）的用户能自行修改适配其设备的vendor.img。  

* 开始刷写：
在主机的终端里，输入以下命令让设备重启到fastbootd模式：
```
adb reboot fastboot
```
注意：请确保设备进入的是 fastbootd 模式（屏幕上会显示fastbootd字样），而不是常规的fastboot（即Bootloader）模式。  
在主机的终端里，导航到包含vendor.img的目录，然后执行刷写命令：  
```
fastboot flash vendor vendor.img
```

##### 2) 刷写vbmeta镜像以禁用AVB
成功刷写vendor.img后，我们需要刷写vbmeta和vbmeta_system两个镜像，以彻底关闭安卓验证启动（AVB, Android Verified Boot）。  
* 定位文件：
这两个文件 (vbmeta.img 和 vbmeta_system.img) 位于文件的 OTA_files/img/original/ 目录中，它们是未经修改的官方文件。  
* 执行刷写：
在fastbootd模式下，执行以下两条命令：  
```
# 关闭主验证链
fastboot --disable-verity flash vbmeta vbmeta.img

# 关闭针对system分区的验证链
fastboot --disable-verity flash vbmeta_system vbmeta_system.img
```
完成以上所有刷写操作后，请不要立即重启设备，继续进行第三步的操作。


## English Version
