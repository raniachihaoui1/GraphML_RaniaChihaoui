# Analysis Summary - Kifaf Towers Floor 04 (Semantic Room Graph + GNN)

_Generated: 2026-06-25 19:53:44_

Kifaf Towers - Floor 04 semantic room graph + GNN room-type prediction.

## Parameters

| Parameter | Value |
|---|---|
| `FLOOR_TAG` | F04 |
| `room_types` | ['bedroom', 'livingroom', 'kitchen', 'corridor', 'stairs', 'bathroom', 'storeroom', 'balcony'] |
| `proximity_tol` | 0.6 |
| `n_classes` | 8 |
| `model` | GraphSAGE (topologicpy.PyG) |

## Room graph

- Rooms (nodes): **127**  |  Edges: **130**  |  Isolated: 0  |  Density: 0.01625

| Room type | Count |
|---|---|
| bedroom | 26 |
| livingroom | 12 |
| kitchen | 12 |
| corridor | 17 |
| stairs | 3 |
| storeroom | 11 |
| bathroom | 34 |
| balcony | 12 |

## Top adjacency types

| Pair | Count |
|---|---|
| bedroom - corridor | 24 |
| corridor - livingroom | 22 |
| bathroom - corridor | 21 |
| balcony - livingroom | 12 |
| corridor - storeroom | 11 |
| bathroom - bedroom | 8 |
| kitchen - livingroom | 7 |
| corridor - corridor | 6 |
| bathroom - livingroom | 5 |
| corridor - kitchen | 5 |
| corridor - stairs | 3 |
| bedroom - livingroom | 2 |

## Centrality (top-5 rooms)

- **Degree**: corridor (0.0635), corridor (0.0635), corridor (0.0635), corridor (0.0556), corridor (0.0556)
- **Closeness**: corridor (0.2229), corridor (0.2094), corridor (0.1969), corridor (0.1945), livingroom (0.1903)
- **Betweenness**: corridor (0.3762), corridor (0.3415), corridor (0.3084), corridor (0.2065), corridor (0.1677)

## Node classification - test metrics

| Metric | Value |
|---|---|
| test_accuracy | 0.8696 |
| test_precision | 0.837 |
| test_recall | 0.8696 |
| test_f1 | 0.8522 |

- Accuracy (all rooms): **0.937**  |  Accuracy (test rooms): **0.8696**

## Per-class accuracy

| Room type | Accuracy |
|---|---|
| bedroom | 1.0 |
| livingroom | 1.0 |
| kitchen | 1.0 |
| corridor | 1.0 |
| stairs | 1.0 |
| storeroom | 0.364 |
| bathroom | 0.971 |
| balcony | 1.0 |

## Misclassified rooms

| Node | True | Pred | Split |
|---|---|---|---|
| 84 | bathroom | storeroom | test |
| 105 | storeroom | bathroom | val |
| 106 | storeroom | bathroom | train |
| 107 | storeroom | bathroom | test |
| 108 | storeroom | bathroom | test |
| 110 | storeroom | bathroom | train |
| 113 | storeroom | bathroom | val |
| 114 | storeroom | bathroom | train |

---
_Generated automatically by section 15 of the notebook._