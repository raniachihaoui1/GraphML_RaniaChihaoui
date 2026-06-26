# Analysis Summary - Kifaf Towers Floor 03 (Semantic Room Graph + GNN)

_Generated: 2026-06-25 23:38:13_

Kifaf Towers - Floor 03 semantic room graph + GNN room-type prediction.

## Parameters

| Parameter | Value |
|---|---|
| `FLOOR_TAG` | F03 |
| `room_types` | ['bedroom', 'livingroom', 'kitchen', 'corridor', 'stairs', 'bathroom', 'storeroom', 'balcony'] |
| `proximity_tol` | 0.6 |
| `n_classes` | 8 |
| `model` | GraphSAGE (topologicpy.PyG) |

## Room graph

- Rooms (nodes): **101**  |  Edges: **102**  |  Isolated: 0  |  Density: 0.0202

| Room type | Count |
|---|---|
| bedroom | 20 |
| livingroom | 9 |
| kitchen | 9 |
| corridor | 15 |
| stairs | 3 |
| storeroom | 10 |
| bathroom | 26 |
| balcony | 9 |

## Top adjacency types

| Pair | Count |
|---|---|
| bedroom - corridor | 19 |
| corridor - livingroom | 18 |
| bathroom - corridor | 17 |
| corridor - storeroom | 10 |
| balcony - livingroom | 9 |
| corridor - corridor | 6 |
| bathroom - bedroom | 5 |
| kitchen - livingroom | 4 |
| bathroom - livingroom | 3 |
| corridor - kitchen | 3 |
| corridor - stairs | 3 |
| bathroom - kitchen | 2 |

## Centrality (top-5 rooms)

- **Degree**: corridor (0.08), corridor (0.08), corridor (0.08), corridor (0.07), corridor (0.07)
- **Closeness**: corridor (0.2474), corridor (0.223), corridor (0.2189), corridor (0.215), livingroom (0.2138)
- **Betweenness**: corridor (0.4136), corridor (0.2968), corridor (0.2826), corridor (0.274), livingroom (0.2079)

## Node classification - test metrics

| Metric | Value |
|---|---|
| test_accuracy | 0.95 |
| test_precision | 0.9583 |
| test_recall | 0.95 |
| test_f1 | 0.9439 |

- Accuracy (all rooms): **0.9307**  |  Accuracy (test rooms): **0.95**

## Per-class accuracy

| Room type | Accuracy |
|---|---|
| bedroom | 1.0 |
| livingroom | 1.0 |
| kitchen | 1.0 |
| corridor | 1.0 |
| stairs | 1.0 |
| storeroom | 0.3 |
| bathroom | 1.0 |
| balcony | 1.0 |

## Misclassified rooms

| Node | True | Pred | Split |
|---|---|---|---|
| 83 | storeroom | bathroom | train |
| 84 | storeroom | bathroom | train |
| 85 | storeroom | bathroom | val |
| 86 | storeroom | bathroom | train |
| 89 | storeroom | bathroom | test |
| 90 | storeroom | bathroom | train |
| 91 | storeroom | bathroom | val |

---
_Generated automatically by section 15 of the notebook._