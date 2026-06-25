# Analysis Summary — Dubai Tower (Level 03 + Level 04)

_Generated: 2026-06-25 14:52:42_

Spatial Intelligence analysis of two stacked residential floors (Level 03 + Level 04) of the Dubai Tower as one connected building.

## Parameters

| Parameter | Value |
|---|---|
| `FLOOR_TAGS` | ['F03', 'F04'] |
| `FLOOR_OBJS` | ['c:\\Users\\Win11\\GraphML_RaniaChihaoui\\assets\\obj\\F03.obj', 'c:\\Users\\Win11\\GraphML_RaniaChihaoui\\assets\\obj\\F04.obj'] |
| `GRID_SIZE` | 1.0 |
| `FLOOR_HEIGHT` | 4.0 |
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
| Level 03 (lower) | 12822 | 1431 |
| Level 04 (upper) | 10526 | 1342 |

## Building graph

- Nodes: **2773**  |  Edges: **4925**  |  Density: **0.00128**  |  Stair edges: **4**

## Minimum Spanning Tree

- Vertices: 2773  |  Edges: 2772  |  Density: 0.00072

## Cross-floor shortest path

- Length: **73.0**  |  Nodes: 71
  |  Simplified: 61.3 (4 waypoints)

## Centrality metrics

| Metric | min | max | mean | std | per-floor mean |
|---|---|---|---|---|---|
| Degree | 0.0004 | 0.0018 | 0.0013 | 0.0002 | [0.001291, 0.001271] |
| Closeness | 0.0171 | 0.0395 | 0.0279 | 0.0048 | [0.027614, 0.02812] |
| Betweenness | 0.0000 | 0.2530 | 0.0130 | 0.0174 | [0.012627, 0.013411] |

**Top 5 degree hubs:**

- Floor 1 (78.37, -3.15) -> 0.0018 (5 connections)
- Floor 1 (94.37, 8.85) -> 0.0018 (5 connections)
- Floor 2 (94.37, 8.85) -> 0.0018 (5 connections)
- Floor 1 (52.37, -17.15) -> 0.0014 (4 connections)
- Floor 1 (53.37, -17.15) -> 0.0014 (4 connections)

## Community detection

- Communities: **28**  |  Total cells: 2773  |  Total area: ~2773.0 m2

| Community | Cells | Area m2 |
|---|---|---|
| 0 | 112 | 112.0 |
| 1 | 99 | 99.0 |
| 2 | 59 | 59.0 |
| 3 | 114 | 114.0 |
| 4 | 110 | 110.0 |
| 5 | 143 | 143.0 |
| 6 | 78 | 78.0 |
| 7 | 64 | 64.0 |
| 8 | 68 | 68.0 |
| 9 | 218 | 218.0 |
| 10 | 136 | 136.0 |
| 11 | 134 | 134.0 |
| 12 | 97 | 97.0 |
| 13 | 136 | 136.0 |
| 14 | 121 | 121.0 |
| 15 | 69 | 69.0 |
| 16 | 57 | 57.0 |
| 17 | 141 | 141.0 |
| 18 | 54 | 54.0 |
| 19 | 67 | 67.0 |
| 20 | 143 | 143.0 |
| 21 | 111 | 111.0 |
| 22 | 66 | 66.0 |
| 23 | 101 | 101.0 |
| 24 | 92 | 92.0 |
| 25 | 68 | 68.0 |
| 26 | 52 | 52.0 |
| 27 | 63 | 63.0 |

## Visibility Graph Analysis (per floor)

| Floor | Viewpoints | min | max | mean |
|---|---|---|---|---|
| Level 03 (lower) | 350 | 11 | 95 | 48.229 |
| Level 04 (upper) | 325 | 8 | 86 | 46.818 |

---
_Generated automatically by section 23. Community colours/partition are stochastic and may differ between runs._