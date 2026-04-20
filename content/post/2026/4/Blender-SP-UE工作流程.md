---
date: '2026-04-12T22:30:33+08:00'
draft: false
title: 'Blender-Substance Painter-UE工作流程'
---

## Blender

1. 建模
2. 展开UV
3. 导出为fbx，记得选择“几何数据->导出平滑组”

额外操作：
- Alt+N：选择翻转面法向
- 编辑模式下，右上角方形格子按钮中开关面法向显示

## Adobe Substance 3D Painter

1. 导入fbx
2. 烘培
3. 绘制材质，可以灵活使用蒙版
4. 用‘Unreal Engine’模板导出贴图

## Unreal Engine

1. 导入fbx（不知为何，有时候模型的面会全都反过来，需要在Blender中先反一次）
2. 导入贴图，贴在Base的材质实例上
3. 贴在模型上

如果从一开始就忘了展开UV，可以在UE中选择“资产操作->导出”，然后再经历Blender->SP的流程。

## 杂记

UE提示“非nanite标记工作队列溢出”，发现是我在场景中使用了过多引擎自带的基本网格体Cube（而它没有开启Nanite）。我将它打开了，暂时没发现什么副作用。
