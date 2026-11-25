# 阶段 3：RHI 与一份 HLSL

大约 6–8 周。你已经能在三个 API 里分别画贴图物体。这一阶段把差异收成一层薄接口，并让三个后端吃同一份着色器源码。

完整引擎的资源和材质系统放到阶段 4。这里的 Demo 只证明：换 API 不换场景，改 HLSL 三个后端一起变。

## 学习目标

- 划出 RHI 的边界。公共类型围绕 Buffer、Texture、Sampler、Shader、Pipeline、CommandList、Swapchain，以及一组绑定调用。
- 把 pass、材质、光源、场景留在 RHI 外面。能举出一条「若放进去，另一个后端会别扭」的接口。
- 定下着色器约定：源码只写 HLSL。Direct3D 12 用 DXIL，Vulkan 用 SPIR-V，OpenGL 由 SPIR-V 得到 GLSL。
- 定下寄存器：`b` 给常量缓冲，`t` 给纹理，`s` 给采样器。在笔记里写明它们落到 OpenGL binding、Vulkan set/binding、Direct3D 12 根参数的规则。
- 动态常量缓冲按帧分开，避免 CPU 写入 GPU 还在读的那一份。

## 学习产出

笔记：

- 一张类型图，每个类型下面最多写 5 个操作。写不下就说明接口太厚。
- 一张「HLSL 到三个后端」的编译路径。
- 一个反例：你曾经想放进 RHI、后来拿掉的函数，以及拿掉的原因。

你自己写的 Demo：

1. `rhi_cube`：一个程序，启动参数选择 `gl`、`vk` 或 `d3d12`。场景是同一个立方体加同一张贴图、同一个 MVP。着色器只有一份 HLSL。
2. 在这个程序里改 HLSL 的颜色或 UV 变换，三个 API 都要出现同样的变化。不要求 PBR，不要求阴影。

窗口、加载模型和数学库由你自己选。选择写进笔记即可。

## 学习安排

- 第 1 周：只写笔记里的类型图和反例，不写后端。周末删掉图上所有「只有某一个 API 才说得通」的名字。
- 第 2–3 周：先接上你最熟的 OpenGL 后端，让 `rhi_cube` 通过这个后端画出来。着色器可以暂时仍是 GLSL，用来把接口跑通。
- 第 4–5 周：接上 HLSL 编译。Direct3D 12 和 Vulkan 两个后端至少一个先通。工作日只处理编译报错，周末只处理画面。
- 第 6 周：第三个后端。
- 第 7–8 周：三个后端的画面并排对比（截图即可），补「改一行 HLSL」的记录。接口若在这周还在膨胀，停下来删，不加功能。

## 学习资源

中文：

- [GAMES104 视频](https://www.bilibili.com/video/BV1oU4y1R7Km/)：第 2 讲架构分层、第 4 讲渲染实践、第 7 讲管线与后处理。用来看引擎里渲染接口放在哪一层。粒子、网络、工具链本阶段不看。
- [HLSL 文档（中文）](https://learn.microsoft.com/zh-cn/windows/win32/direct3dhlsl/dx-graphics-hlsl)
- 阶段 2 的对照笔记。接口设计以那份对照表为输入。

英文（编译工具链以官方仓库为准）：

- [DirectXShaderCompiler](https://github.com/microsoft/DirectXShaderCompiler)
- [SPIRV-Cross](https://github.com/KhronosGroup/SPIRV-Cross)
- [vkguide 里关于描述符和管线的章节](https://vkguide.dev/)

## 验收标准

- `rhi_cube` 三个 API 都能运行，截图里的立方体姿态和贴图一致。
- 改 HLSL 后，你有一次记录说明三个后端都变了，而且没有改三份着色器。
- 类型图上没有 G-Buffer、阴影 pass、材质这些词。
- 你能讲清多帧并行时，立方体的 MVP 缓冲为什么不能只有一份还每帧覆写。

## 自测

闭卷画图，30 分钟。

1. 一次绘制，从绑定管线到画出索引，中间要设置哪些 RHI 调用？
2. `register(b0)`、`register(t0)`、`register(s0)` 在三个后端各变成什么？
3. OpenGL 后端如果把 `glUniform` 按名字设置暴露成公共接口，Vulkan 后端会被迫做什么？
4. 交换链图像算不算一张普通 Texture？你的接口怎么避免每帧都让上层去处理「这张图当前能不能写」？
5. 列出三个明确不放进 RHI 的类型，并各给一句理由。

## 复盘

- 三个后端各花了几周。最亏时间的是编译链、同步，还是绑定。
- 贴三张截图的文件名。
- 删掉的那个接口是什么。
- 带进阶段 4 的限制写清楚，例如「立方体还是写死的」「还没有资源缓存」。这些限制就是阶段 4 的起点。
- 进入阶段 4 的条件：验收四条都达到。缺一个后端就不要开始堆 PBR。

## 还没搞懂的问题

-
