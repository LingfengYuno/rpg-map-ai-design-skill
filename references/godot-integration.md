# Godot 4.x 地图接入参考

先读取目标项目现有的地图场景、玩家脚点、交互、出生点、场景切换和存档方式。下列结构是没有既有规范时的示例，不应替换已工作的架构。

```text
Map (Node2D)
├── Background (Sprite2D)
├── Boundaries (Node2D)
│   └── StaticBody2D + CollisionPolygon2D
├── YSortRoot (Node2D, y_sort_enabled = true)
│   ├── OccludingObject (Node2D, 原点为地面接触点)
│   │   ├── Sprite2D
│   │   └── StaticBody2D + CollisionShape2D
│   └── Player
└── UILayer (CanvasLayer，可选)
```

统一使用 `world_position = source_pixel * world_scale + background_origin_adjustment` 换算底图、遮挡、碰撞和标记；先确定 `Sprite2D.centered` 与节点原点。相同来源的图层保持相同画布和缩放。`objects` 中的 `footprint` 对应地面碰撞，`sort_anchor` 对应 Y 排序基准；高于地面的立面像素可向上延伸，不属于地面占地。

编辑器中的 `doors` 只是开口标记；实际碰撞要在开口处断开。出生点由最终 `walkable` 与玩家碰撞体共同决定，须避开边界和物件。出口使用项目已有的地图切换机制。需要互动的目标应复用项目当前的交互入口；一次性事件按项目已有持久状态机制处理。

验证时依次检查：资源导入与场景加载、玩家出生、必经通路、开口、物件碰撞、前后左右遮挡、目标交互和往返场景切换。静态脚本检查只能证明一部分；实际走动与视觉结果需另行确认。
