# AK-SP3000-Root-Guide

Rooting the Astell&amp;Kern SP3000 with Apatch

## [Chinese Version](https://github.com/PPPPatrick0/AK-SP3000-Root-Guide/edit/main/README.md#%E7%AE%80%E7%9F%AD%E8%AF%B4%E6%98%8E)
## [English Version]()

### 简短说明

* 本指南旨在为艾利和 (Astell&Kern) SP3000 播放器提供一套详尽且经过验证的Root方案。本方案基于 SP3000 （无印版）、系统版本 v1.51 。

* 理论上，类似机型如 SP3000T 等，也可参照本指南的核心思路进行尝试，但操作者需自行承担因设备或系统版本差异带来的未知风险。

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
* 核心刷写: 使用fastboot工具，将预先准备好的定制化启动镜像 (boot.img)、供应商镜像 (vendor.img) 及 vbmeta.img 刷入您的设备。
* 安装管理器: 安装APatch管理器 (KernelPatch)，用于后续的Root权限管理。

#### 准备工作
在设备上启用开发者选项，并打开USB调试权限。  
由于SP3000没有在常规设置中提供开启ADB调试的入口，我们需要通过一个终端命令来调用其隐藏的工厂调试菜单。

##### 1 安装终端模拟器
在文件中，找到 APK/ 目录。  
定位到 Term_Modded(uk.co.sevendigital.android).apk 这个文件。这是一个经过修改的终端模拟器应用。  
将此APK文件传输到您的SP3000设备内部存储中，并进行安装。

##### 2 获取完整的读写权限
Astell&Kern的系统存在一个关于文件权限的特殊Bug。为了确保终端应用能正常工作，请遵循以下步骤：  
· 首次安装后：启动一次刚刚安装的终端应用。如果系统提示请求文件读写权限，请务必“允许”。  
· 退出应用。  
· 再次安装：使用任意方式，找到并重新安装一次 Term_Modded 应用（覆盖安装即可）。  
· 再次启动终端应用。如果系统再次提示请求权限，请同样“允许”。  

##### 3 开启ADB调试
在终端模拟器应用中，输入以下命令，然后按回车键：  
```
am start --user 0 -n com.iriver.tester.factorytool/.DebugSettingActivity
```
执行后，您将直接进入Astell&Kern的工厂调试界面。  
在此界面中，找到 ADB Debug 选项，并将其开启。  

##### 4 验证连接
现在，使用USB数据线将您的SP3000连接到Linux主机。  
在主机的终端里，输入以下命令：
```
adb devices
```
如果您看到输出了您设备的序列号，并且状态为 device，则表示ADB连接已成功建立。

##### 5 （可选，但强烈推荐）永久开启ADB调试
默认情况下，每次重启SP3000后，ADB调试选项都会自动关闭。  
为了方便后续操作及日后使用，您可以将其设置为永久开启。  
在主机的终端里，输入以下命令：
```
adb shell am start --user 0 -n com.iriver.tester.factorytool/.UserDebugActivity
```
设备的屏幕上会再次出现一个工厂调试界面。  
点击此界面上的第一个选项（与ADB相关的选项）。  
完成此操作后，ADB调试模式将会被永久激活，即使设备重启也不会关闭。


