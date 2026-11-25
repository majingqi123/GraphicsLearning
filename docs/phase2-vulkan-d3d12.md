# 阶段 2：Vulkan 与 Direct3D 12

大约 12–14 周。目标不是把阶段 1 的 PBR 重写一遍，而是让两个显式 API 的同步、资源和绑定在你手里变成同一套说法。

做法：每个小功能先写 Vulkan，下一周用 Direct3D 12 做同一个功能，周末写对照笔记。不要两个 API 在同一个晚上一起新开。

## 学习目标

- 说清交换链、命令缓冲、管线状态对象为什么要提前建好。
- 说清一帧里谁在等谁：Vulkan 的 semaphore 与 fence，Direct3D 12 的 fence。frames in flight 先用 2 或 3。
- 对照描述符：Vulkan 的 descriptor set；Direct3D 12 的描述符堆和根签名。
- 写得出资源屏障两边的状态：谁写完，谁接着读。能预测拿掉屏障后的典型错误。
- 知道内存类型和 heap 的差别，以及 VMA、D3D12MA 替你选了什么。这一阶段可以先手写一块 staging 上传，分配器作为阅读对象，不要求先封装库。
- 会打开 Vulkan validation layer，会用 PIX 看一次 Direct3D 12 的一帧。

## 学习产出

对照笔记：每个概念一行「Vulkan 里叫什么 / Direct3D 12 里叫什么 / 缺了会怎样」。

你自己写的 Demo。每个功能两个程序，或同一个仓库里两个目标，但要能分别运行：

1. `clear`：创建窗口和交换链，清成一种不纯黑的颜色。
2. `triangle`：一个三角形。
3. `mvp`：常量缓冲里的 MVP，三角形或立方体随时间或相机变化。
4. `texture`：一张贴图采样到几何体上。
5. `offscreen`：先画到离屏颜色目标，再采样到交换链上。
6. `compute`：一次 dispatch，把一张图清成渐变，或对 `offscreen` 的结果做一次模糊，然后再显示。

最后再加一个不看教程重写的 `textured_triangle`：只保留你自己的笔记，Vulkan 和 Direct3D 12 各写一份。这是验收程序，不要从前面的工程里直接拷贝后改名。

## 学习安排

- 第 1–3 周：Vulkan 的 `clear` 和 `triangle`。第 1 周允许大部分时间耗在实例、设备和交换链上。
- 第 4–5 周：Direct3D 12 的 `clear` 和 `triangle`。对照着写同步，不要重新学一遍「什么是三角形」。
- 第 6–7 周：两边的 `mvp`。
- 第 8–9 周：两边的 `texture`，包括 staging 上传。
- 第 10–11 周：两边的 `offscreen`。
- 第 12 周：两边的 `compute`，以及一次故意写错的屏障，把 validation 或 PIX 的报错抄进笔记，然后改回来。
- 第 13–14 周：闭卷重写 `textured_triangle`。超时就只重写这一个，不回阶段 1 加效果。

工作日适合读一节「这一章在解决交换链的哪一步」。周末适合把程序跑起来并写对照。

## 学习资源

Vulkan，先中文再英文：

- [Vulkan Tutorial 中文仓库](https://github.com/fangcun010/VulkanTutorialCN)（原文 [vulkan-tutorial.com](https://vulkan-tutorial.com/)）。用它建立「从设备到交换链」的完整链条。三角形之前的章节会很长，按周拆开，不要一天读完。
- [Vulkan Guide 中文](https://guide.vulkan.net.cn/)（原文 [vkguide.dev](https://vkguide.dev/)）。三角形跑通之后再用它看更接近引擎的写法：动态渲染、网格、描述符。中文若和英文示例对不上，以英文站的代码结构为准。

Direct3D 12：

- 书：《DirectX 12 3D 游戏开发实战》，Frank D. Luna 著，王陈译。重点读初始化、命令列表、同步、根签名、常量缓冲、纹理、计算着色器这几章。光照章可以快读，你在阶段 1 已经写过。
- [Direct3D 12 编程指南（中文）](https://learn.microsoft.com/zh-cn/windows/win32/direct3d12/directx-12-programming-guide)
- [DirectX-Graphics-Samples](https://github.com/microsoft/DirectX-Graphics-Samples)
- [PIX 介绍](https://devblogs.microsoft.com/pix/download/)

概念对照时，RenderDoc 对两个 API 都能抓。[RenderDoc](https://renderdoc.org/)

## 验收标准

- 合上教程写出的 `textured_triangle`，Vulkan 和 Direct3D 12 都能看到贴图，并且 MVP 在起作用。
- 笔记里的对照表覆盖：交换链、fence/semaphore、管线状态、描述符、命令缓冲、屏障、上传。
- 你能指出自己那次故意写错的屏障，报错或花屏对应的是「写后读」还是「读后写」。
- 能回答：为什么这两个 API 不能在绘制到一半时换成另一个着色器，而要换一整条管线。

## 自测

闭卷 40 分钟。可以画时序，不要求默写函数名。

1. 画出 2 帧并行时，CPU 写第 2 帧的常量缓冲之前要等哪一次 GPU 完成。
2. semaphore 和 fence，哪一个更适合「GPU 队列之间」的顺序，哪一个更适合「CPU 等 GPU」？Direct3D 12 用什么对象承担这两件事？
3. 根签名和 descriptor set layout 各在约束什么？它们为什么通常不在每帧重建？
4. 一张纹理从磁盘到像素着色器，至少经过哪两次状态变化？
5. 交换链的图像，Present 之后你是否还可以把它当渲染目标写？要怎样才能再写？
6. 验证层报的是 API 使用错误。它不负责告诉你 PBR 公式写错了。举一个只有抓帧才能发现、验证层不会报的问题。

第 1、2、4 题写不清楚，就还停在本阶段。

## 复盘

- Vulkan 第一次画出三角形用了几周，Direct3D 12 用了几周。第二遍应该明显更短，如果没有，写出卡在同步还是卡在绑定。
- 对照表放在笔记最前面，后面的 Demo 只追加，不另起一套名词。
- 抄一条真实的 validation 或 PIX 信息，以及你的修改。
- 带进阶段 3 的问题应该变成「这个差异要不要出现在公共接口上」，而不是「函数名是什么」。
- 进入阶段 3 的条件：闭卷版 `textured_triangle` 两边都在，第 1、2、4 题能讲通。

## 还没搞懂的问题

-
