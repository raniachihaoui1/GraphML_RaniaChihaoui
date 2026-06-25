# Analysis Summary — Dubai Tower (Level 03 + Level 04)

_Generated: 2026-06-25 07:20:23_

Spatial Intelligence analysis of two stacked residential floors (Level 03 + Level 04) of the Dubai Tower as one connected building.

## Parameters

| Parameter | Value |
|---|---|
| `FLOOR_TAGS` | ['F03', 'F04'] |
| `FLOOR_OBJS` | ['c:\\Users\\Win11\\GraphML_RaniaChihaoui\\assets\\obj\\F03.obj', 'c:\\Users\\Win11\\GraphML_RaniaChihaoui\\assets\\obj\\F04.obj'] |
| `GRID_SIZE` | 1.5 |
| `FLOOR_HEIGHT` | 10.0 |
| `FLOOR_LEVELS` | [0, 1] |
| `PLAN_AXES` | ['X', 'Y'] |
| `VGA_GRID_SIZE` | 2.0 |
| `VIS_SAMPLES` | 12 |
| `ISO_STEP` | 2.0 |
| `n_stair_locations` | 4 |
| `stairs_auto_placed` | True |

## Geometry

- **Plan bounding box:** X [46.37, 113.05], Y [-19.15, 18.75]  (66.68 x 37.90 units)

| Floor | Faces | Navigable nodes |
|---|---|---|
| Level 03 (lower) | 12822 | 601 |
| Level 04 (upper) | 10526 | 595 |

## Building graph

- Nodes: **1196**  |  Edges: **2001**  |  Density: **0.00280**  |  Stair edges: **4**

## Minimum Spanning Tree

- Vertices: 1196  |  Edges: 1194  |  Density: 0.00167

## Cross-floor shortest path

- Length: **79.0**  |  Nodes: 48
  |  Simplified: 69.7 (4 waypoints)

## Centrality metrics

| Metric | min | max | mean | std | per-floor mean |
|---|---|---|---|---|---|
| Degree | 0.0008 | 0.0042 | 0.0028 | 0.0006 | [0.002759, 0.002841] |
| Closeness | 0.0008 | 0.0526 | 0.0395 | 0.0069 | [0.039387, 0.039551] |
| Betweenness | 0.0000 | 0.2672 | 0.0209 | 0.0249 | [0.020318, 0.021502] |

**Top 5 degree hubs:**

- Floor 1 (62.87, -7.15) -> 0.0042 (5 connections)
- Floor 1 (52.37, -16.15) -> 0.0033 (4 connections)
- Floor 1 (53.87, -16.15) -> 0.0033 (4 connections)
- Floor 1 (55.37, -16.15) -> 0.0033 (4 connections)
- Floor 1 (56.87, -16.15) -> 0.0033 (4 connections)

## Community detection

- Communities: **27**  |  Total cells: 1196  |  Total area: ~2691.0 m2

| Community | Cells | Area m2 |
|---|---|---|
| 0 | 46 | 103.5 |
| 1 | 42 | 94.5 |
| 2 | 37 | 83.25 |
| 3 | 55 | 123.75 |
| 4 | 21 | 47.25 |
| 5 | 40 | 90.0 |
| 6 | 81 | 182.25 |
| 7 | 35 | 78.75 |
| 8 | 74 | 166.5 |
| 9 | 45 | 101.25 |
| 10 | 42 | 94.5 |
| 11 | 51 | 114.75 |
| 12 | 30 | 67.5 |
| 13 | 2 | 4.5 |
| 14 | 57 | 128.25 |
| 15 | 46 | 103.5 |
| 16 | 29 | 65.25 |
| 17 | 82 | 184.5 |
| 18 | 64 | 144.0 |
| 19 | 50 | 112.5 |
| 20 | 42 | 94.5 |
| 21 | 44 | 99.0 |
| 22 | 40 | 90.0 |
| 23 | 42 | 94.5 |
| 24 | 22 | 49.5 |
| 25 | 47 | 105.75 |
| 26 | 30 | 67.5 |

## Visibility Graph Analysis (per floor)

| Floor | Viewpoints | min | max | mean |
|---|---|---|---|---|
| Level 03 (lower) | 350 | 11 | 95 | 48.229 |
| Level 04 (upper) | 325 | 8 | 86 | 46.818 |

---
_Generated automatically by section 23. Community colours/partition are stochastic and may differ between runs._