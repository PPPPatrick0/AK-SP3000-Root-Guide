# AK-SP3000-Root-Guide
Rooting the Astell&amp;Kern SP3000 with Apatch

## [Chinese Version](https://github.com/PPPPatrick0/AK-SP3000-Root-Guide/edit/main/README.md#%E7%AE%80%E7%9F%AD%E8%AF%B4%E6%98%8E)
## [English Version]()

### 简短说明

* 本指南旨在为艾利和 (Astell&Kern) SP3000 播放器提供一套详尽且经过验证的Root方案。本方案基于 SP3000 （无印版）、系统版本 v1.51 。

* 理论上，类似机型如 SP3000T 等，也可参照本指南的核心思路进行尝试，但操作者需自行承担因设备或系统版本差异带来的未知风险。

### 重要提示：

**风险警告: **

* Root过程可能将伴随着失去保修、数据丢失甚至设备永久损坏（变砖）的风险。请您在充分理解所有步骤和潜在风险后再进行操作。所有操作均由您本人负责。

环境要求: 由于在Windows下，Google USB驱动程序可能存在兼容性问题，导致设备在特定模式（如fastbootd）下无法被主机识别。因此，本指南强烈建议并要求在Linux环境下完成所有刷写操作，以确保流程的顺利进行。
操作流程概览 (Process Overview)
本教程将引导您完成以下三个核心步骤：
准备工作: 在设备上启用开发者选项，并打开USB调试权限。
核心刷写: 使用fastboot工具，将预先准备好的定制化启动镜像 (boot.img)、供应商镜像 (vendor.img) 及 vbmeta.img 刷入您的设备。
安装管理器: 安装APatch管理器 (KernelPatch)，用于后续的Root权限管理。
