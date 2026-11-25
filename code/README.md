# 代码与 Demo

计划在 [docs](../docs)，每周笔记在 [notes](../notes)。这里只放你自己写的 C++ 程序。一个 Demo 一个目录，能单独运行。

```text
code/
  phase0-math/
    math_spaces/
    soft_raster/
  phase1-opengl/
    01_mesh/
    02_blinn/
    03_tonemap/
    04_shadow/
    05_pbr/
    06_deferred/
    07_cull/
    reference/
  phase2-vulkan/
    clear/
    triangle/
    mvp/
    texture/
    offscreen/
    compute/
    textured_triangle/
  phase2-d3d12/
    clear/
    triangle/
    mvp/
    texture/
    offscreen/
    compute/
    textured_triangle/
  phase3-rhi/
    rhi_cube/
  phase4-engine/
  phase5-interview/
    shadow_bias/
    gbuffer_cost/
    sync_bug/
    frames_in_flight/
```

阶段 2 的 Vulkan 和 Direct3D 12 分开，避免两个 API 的工程缠在一起。阶段 4 是同一个引擎，用启动参数选 API。目录在你开始写那个 Demo 时再建立，名字与上表一致，周记里写明怎么运行。
