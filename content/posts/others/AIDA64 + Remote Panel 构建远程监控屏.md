---
title: AIDA64 + Remote Panel 构建远程监控屏
slug: building-remote-monitoring-screens
categories: 杂谈
date: 2024-11-20
---
## 前言

换了台新手机，旧的 “华为畅想Z（2021）” 想着不能放在旁边吃灰，刚好在网上又刷到了有关电脑副屏视频，于是自己也跟着做了一个。

成果展示：

![示例](https://cdn.img.turingzy.cn/2026/20260418194339429.jpg)

## 操作

### 安装软件

![示例 | 700](https://cdn.img.turingzy.cn/2026/20260418194339430.png)

1. AIDA64 是一款测试软硬件系统信息的工具，它可以详细的显示出 PC 的每一个方面的信息。AIDA64 不仅提供了诸如协助超频，硬件侦错，压力测试和传感器监测等多种功能，而且还可以对处理器，系统内存和磁盘驱动器的性能进行全面评估。
2. Remote Panel 是一款免费的 Android 应用，可以让您用旧的 Android 设备显示 Windows PC 的系统信息，无需 WiFi 连接。
3. 安卓 ADB 驱动是一款专用于安卓手机的驱动程序，安装安卓 adb 驱动后可有效解决手机 usb 连接电脑出现的异常问题。

简单来说，我们需要使用 AIDA64 获取我们 PC 的各种硬件信息，然后 Remote Panel  通过 USB 连接来查看这些信息，屏幕主题则是用来将这些硬件信息更好得展示出来。

ADB 驱动，可以直接去官网下载最新版，解压，配置环境变量即可。（具体操作看最后的参考资料）

### Remote Panel 配置

安装完成后，打开手机版的 Remote Panel ，长按出现以下界面，勾选 Enable Fullscreen 进入全屏模式。

![示例 | 700](https://cdn.img.turingzy.cn/2026/20260418194339431.jpg)

不同手机得开发者模式打卡方式不一样，我这台华为是在 【设置】-【关于本机】中，连续点击**系统版本号**直到出现开发者模式开启提示。然后在【系统和更新】中找到开发人员选项，进入后开启 USB 调式、“仅充电“模式下允许 ADB 调试、充电时屏幕不休眠、充电温度限制。

完成以上操作后通过 USB 数据线连接电脑，电脑端打开 Remote Panel，在右下角任务栏中右键，点击 Settings

![示例 | 400](https://cdn.img.turingzy.cn/2026/20260418194339432.png)

在 Device serial 中下拉选框选择手机，只修改 Device serial ，其他不变。

![示例 | 700](https://cdn.img.turingzy.cn/2026/20260418194339433.png)

完成后，点击【Check for new device now】，右下角提示已连接到某设备，则代表 Remote Panel 配置成功。
### AIDA64 配置

打开 AIDA64，点击左上角文件 -> 设置

![示例](https://cdn.img.turingzy.cn/2026/20260418194339434.png)

在左边的状态栏点击 LCD 选项，在右边窗口中选择 Odospace，ip 地址和 TCP / IP 端口都不变，修改 LCD 分辨率为手机分辨率，点击启用 Odospace LCD 支持，LCD 背景色可根据喜好修改。

![示例 | 700](https://cdn.img.turingzy.cn/2026/20260418194339435.png)

点击左边的 LCD 项目，点击右上角的导入，屏幕主题中选择合适得分辨率然后解压即可。可能需要修改文件后缀为 oslcd。

![示例|700](https://cdn.img.turingzy.cn/2026/20260418194339436.png)

导入后：

![示例 | 700](https://cdn.img.turingzy.cn/2026/20260418194339437.png)

如果又出现 `N / A` 的情况则说明AIDA版本有问题，无法获取到相应的设备信息，可以选择升级或者切换其他可以查看的硬件信息。修改后点击右下角的应用即可。

![示例 | 400](https://cdn.img.turingzy.cn/2026/20260418194339438.png)

打开手机的 Remote Panel ，手机上应该就显示出相应界面了。

再做一下 AIDA64 的基本配置，文件 -> 设置 -> 常规，勾选以下选项，然后点击应用。这样每当电脑启动时，自启动 AIDA64，最小化到任务栏。

![示例 | 400](https://cdn.img.turingzy.cn/2026/20260418194339439.png)

最后，为了更好的保护手机，防止长时间充电导致电池鼓胀，在设置中开启充峰值限制，例如电池充电至 `80%` 停止，于 `20%` 再开启充电。（根据自己手机进行设置）

## 参考

- [旧手机再利-手把手教你制作电脑监控屏](https://www.bilibili.com/video/BV1zq4y1a7Xu/?share_source=copy_web&vd_source=7341c7fca3b496e9108bb1fd49c634ef)
- [Cusox の Blog](https://blog.cusox.net/posts/aida64-remote-panel/)
- [如何在 Windows 11 上下载和安装 ADB 驱动程序](https://www.yundongfang.com/Yun144734.html)