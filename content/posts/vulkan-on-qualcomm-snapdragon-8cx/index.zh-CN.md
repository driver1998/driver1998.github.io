+++
title = '高通骁龙 8cx 上的原生 Vulkan'
date = 2024-05-23T00:22:48+08:00
categories = ['Windows', 'ARM64', 'Vulkan']
+++

骁龙 X 系列终于发布了，新平台的 GPU 驱动终于包含对 Vulkan API 的原生支持。

有意思的是，该 Vulkan 驱动可以直接移植到老的 8cx 平台上使用，下面介绍一下方法。

这个方法不需要修改驱动文件，也不需要关闭安全启动/驱动签名验证。

## 注意

如果你能够使用微软的 Vulkan 兼容包（基于 Mesa dozen Vulkan 到 DirectX 12 转译层），最好还是用它。强行在老设备上使用骁龙 X 的 Vulkan 驱动非常不稳定，特别是 Vulkan 窗口位于后台的时候。

当然 Vulkan 兼容包只支持 8cx Gen 3，如果你跟我一样用的是 Gen 2，那还是继续往下看吧。

## 下载骁龙 X 的 GPU 驱动

首先我们需要一份骁龙 X 的 GPU 驱动。幸运的是  [WOA Project](https://github.com/WOA-Project) 维护了一系列的高通 SoC 公版驱动包，这些驱动来自高通的参考设计设备。

其中骁龙 X 对应的路径在 https://github.com/WOA-Project/Qualcomm-Reference-Drivers/tree/master/8380_CRD 。

下面有很多个不同的版本，选 200.0.18.0（目前已知能工作的最新版本），然后下载里面的 `qcdx8380.cab`。

## 解压并提取必要文件

下载之后解压，并提取以下文件到一个单独目录（比如 `C:\qcvk`）：

```
adreno_utils.dll
libgsluser.dll
qcgpuarm64xcompilercore.DLL
qcvkarm64xcompiler.dll
qcvkarm64xum.dll
qcvk_icd_arm64x.json
```

## 将 Vulkan ICD 注册到系统

在注册表 `HKEY_LOCAL_MACHINE\SOFTWARE\Khronos\Vulkan\Drivers` 下（没有的键自己创建即可）新建 DWORD，名字为 `qcvk_icd_arm64x.json` 文件的全路径，（如 `C:\qcvk\qcvk_icd_arm64x.json`），值为 0。

![在注册表中注册 ICD](registry.png)

## 获取 Vulkan Loader

如果你使用的是 24H2（26100），系统应该已经自带了 Vulkan Loader `C:\Windows\system32\vulkan-1.dll`，你可以跳过这一节。

否则，可以直接去安装 LunarG 的 Vulkan Runtime，官网已经提供了 ARM64 版本。

## 然后就完成了！

没错，就这么简单。

现在你应该可以尝试运行使用 Vulkan 的程序了，以下是 ARM64 PPSSPP 在 8cx Gen 2 上使用 Vulkan 运行 Yellow Rose of Texas 演示程序的截图。

![PPSSPP 截图 1](ppsspp-1.png)

![PPSSPP 截图 2](ppsspp-2.png)

## 致谢

[Gustave Monce (gus33000)](https://github.com/gus33000) 的概念验证，他将这个驱动移植到了骁龙 855 手机。
