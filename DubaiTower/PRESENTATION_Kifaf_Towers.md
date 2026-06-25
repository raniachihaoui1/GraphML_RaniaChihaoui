# KIFAF TOWERS — Graph Machine Learning
### Presentation script / slide content
**Atkins Engineering, Dubai, UAE · MaCAD 2025/26 · IAAC**
Students: Rania Chihaoui & Mahmoud Mohamed · Professors: Wassim Jabi, Olga Poletkina

> How to use this file: each block below = one slide. **Title** is the slide heading,
> **Show** is the figure/output to place, **Say** is the speaker note (the *why it matters /
> what we prove / what we conclude*). Slides marked **[NEW]** are additions to the WIP deck;
> slides marked **[UPDATE]** replace a 2D-only slide with the 3D multilevel result.
> The thread running through every slide is the question in slide 0.

---

## 0. The question (one-line thesis — say it out loud at the start)
**Can graph machine learning read a real, messy high-rise the way it reads a clean dataset —
both *understand* how the building works as a circulation network, and *predict* what each
room is from layout structure alone?**
We test this on one real tower (Kifaf), encoded into the **Modified Swiss Dwellings (MSD)**
graph standard, with two complementary studies: **Spatial Intelligence** (how the building
flows) and **Node Classification** (can a GNN infer room function).

---

# SECTION 01 — MODIFIED SWISS DWELLINGS (the dataset & standard)

## 1. Title
- KIFAF TOWERS · Graph Machine Learning · the cover graph is our own tower encoded as a graph.

## 2. The team
- Rania Chihaoui (Architect, Tunisia / Spain) · Mahmoud Mohammed (Architect, Egypt / UAE).

## 3. Overview (6 chapters)
01 Modified Swiss Dwellings · 02 Floor-Plan Representation · 03 Graph Construction ·
04 Spatial / Graph Analysis · 05 Node Classification · 06 Limitations & Improvements.
- **Say:** chapters 01–03 build the representation, 04–05 are the two analyses, 06 is honest reflection.

## 4–5. The dataset — Modified Swiss Dwellings
- Origin: first European large-scale floor-plan dataset (Swiss Dwellings v3.0.0). 5,372 plans,
  2–9 units/floor, ~7 rooms/unit. Ships as `networkx.Graph()` **and**
  `torch_geometric.data.Data()` — image, geometry & graph modalities.
- **Why it matters:** MSD is the *benchmark* and the *schema*. By forcing our tower into the
  exact same node/edge CSV schema and label set, our building becomes directly comparable to
  thousands of European apartments — and any model/技术 that works on MSD works on Kifaf.
- **What we prove here:** a Gulf high-rise (very different typology from Swiss housing) can be
  expressed in this standard at all.

## 6. The process — From Floor to Graph
Floor-plan input → room = node → connection = edge → graph → node features → zone/type →
**predict room**.
- **Say:** this single pipeline is the spine of the whole project. Everything after is either
  *building* this pipeline for Kifaf or *running analyses* on its output.

---

# SECTION 02 — FLOOR-PLAN REPRESENTATION (the building & the levels)

## 7–8. The building choice — Kifaf Development Complex
- 3 basements · GF + mezzanine · 2 podium floors · podium deck (3rd floor) · **40 residential
  floors (from 4th)** · roof. Structurally unified by **4 shared elevator cores** + per-unit
  private/service stairs.
- **Why it matters:** the *core* is the hero of the story. A 40-floor tower is one connected
  system only because of the cores — and the graph will show exactly that.

## 9. Unit typology (vertical zoning)
- Basement = parking · GF/Podiums/3rd = services + amenities · 4th–17th = apartments ·
  18th amenities · 19th–42nd apartments · roof.
- **Say:** we deliberately pick a *vertical slice* that spans different functions so the graph
  captures real diversity, not one repeated floor.

## 10. Floor plans — scope of the spatial study
- **3rd Floor (Podium Deck)** and **4th Floor** are the typical residential plates we analyse.
- **[UPDATE] Say:** we have since extended this to a **4-level vertical stack — Ground,
  Podium, 3rd, 4th — connected through the cores** (see Section 04). The 2D per-floor slides
  remain valid as the per-level view; the 3D multilevel is the building-scale view.

## 11–12. 3rd & 4th floor plans (annotated)
- Legend: 1/2/3-bedroom, amenities, balcony, circulation, F-lift, P-lift, services, stair.
- **Why it matters:** these colour categories *are* our ground-truth labels. The honesty of the
  whole ML result depends on this hand-classification being correct.

---

# SECTION 03 — GRAPH CONSTRUCTION (two graphs, two purposes)

> Key idea to state clearly: **we build TWO different graphs from the same plans**, because the
> two questions need different representations.

## 13. Initial setup — edges, nodes, grid (the *navigation* graph)
- 4 panels: 4th, 3rd, Podium 2, Ground. Each floor is sliced into a fine **navigable grid**;
  every walkable cell is a node, 4-neighbours are edges.
- **Why it matters:** this grid graph answers *spatial* questions (how do people move, what is
  central, where are choke points). It is geometry-driven, agnostic to room labels.
- **What we prove:** even an irregular Gulf plate reduces cleanly to a navigable lattice.

## 23. The encoding (the *semantic* graph)
- **Node attributes:** Room Type (9 classes — bedroom, kitchen, bathroom, living room,
  corridor, stairs, outdoor, storage, other) · Zone Type (4 zones — Living, Dynamic, Static,
  Functional). **Edge attributes:** shared opening (door / window / aperture).
- **Why it matters:** this graph answers *semantic* questions (what is each room). Here a node
  is a *whole room*, an edge is a *real door* between two rooms — the MSD schema exactly.
- **Say:** Section 04 uses the grid graph; Section 05 uses this room graph.

## 24. From Rhino to MSD-ready OBJs (the data-prep workflow)
- Rhino layer standard (8 MSD types) + custom extensions (staircase, lift, mep, maid_room,
  aperture, window). Object naming `F04_401_bedroom_master` (floor · apt · room subtype).
  Tower built by stacking one typical floor at the true floor height; export `rooms.obj`,
  `core.obj`, `doors.obj`, `win.obj` with fixed settings (objects named, vertex welding).
- **Then aligned to MSD:** Rhino layer → MSD label (e.g. living+dining → living_room (0),
  bathroom-1 → bathroom (3), c-stairs → corridor (4), maidroom → storage (6)); dataset matched
  to `nodes.csv` (1 row/room, 8 one-hot features), `edges.csv` (1 row/door connection),
  `graphs.csv`, label col = MSD type index 0–7.
- **Why it matters:** this is the un-glamorous 80% of the work — turning architectural geometry
  into a clean, label-aligned dataset. It is also the part most reusable on the next building.

## 25. Closed room volumes → cells
- Each room exported as a **closed watertight volume**; rebuilt into a topologic `Cell`.
  Console shows every apartment room (`F04_401_balcony`, `…_living_room`, `…_bathroom_ensuite`,
  …) with its volume; 384 cells total across the stacked tower in the dataset build.
- **Why it matters:** rooms are solids that share walls; the door geometry is what bridges them.
  This is why edges come from doors, not from touching faces.

## 26–27. The 3D room graph (geometry ↔ graph)
- Left: room cells in 3D · Right: the same rooms as a node-link graph (nodes at room centroids,
  edges = doors), then the circulation edges only.
- **Say:** this is the "aha" image — the building literally becomes a network you can compute on.

---

# SECTION 04 — SPATIAL / GRAPH ANALYSIS (how the building flows)

> Run on the **navigation grid graph**. Per-floor panels (2D) + the building-scale 3D multilevel.

## 14. Spatial graph analysis — definitions
- **Degree** = which space has the most direct connections. **Closeness (integration)** = which
  space is easiest to reach from everywhere. **Betweenness (choice)** = which space the most
  shortest paths pass through. **Community** = densely-linked clusters of space.
- **Why it matters:** these four are space-syntax in graph form — they turn "feel" about a plan
  into measurable, comparable numbers.

## 15. Shortest path
- Cross-floor route from one end to the far end, climbing through the core. Per floor + multilevel.
- **What we prove:** the building is genuinely *one* connected system — a path exists from the
  Ground floor up to the 4th *only* through the cores.

## 16. Closeness / Integration
- Heatmaps: bright = most integrated. The **central corridor + core spine lights up** on every
  residential plate; the deep-plan podium/ground integrate around their centre.
- **Conclude:** integration concentrates on the circulation spine — exactly where a designer
  would expect the "public" life of the floor to be.

## 17. Betweenness / Choice
- The **core/corridor is a sharp bright line** — almost every trip passes through it.
- **Conclude:** the cores are the single point of dependence (and of vulnerability). This is the
  quantitative version of "the whole tower hangs off 4 cores."

## 18. Community detection
- Communities recover **apartment-sized clusters** (and on podium/ground, zone-sized blocks).
- **What we prove:** an unsupervised graph algorithm rediscovers the architect's unit boundaries
  from connectivity alone — strong evidence the graph encodes real spatial structure.

## 19. Community — areas **[fill the empty WIP slide]**
- Table/bars: per-community **cell count and floor area (m²)** for each level.
- **Say:** translating communities into m² makes them legible to architects (e.g. "community 3 ≈
  a 95 m² 2-bed unit"). *(Numbers come from section 18/22 of the spatial notebooks.)*

## 20. Degree centrality
- Corridor/core nodes carry the highest degree; dead-end rooms the lowest.

## 21. Isovists
- Ray-cast visibility polygons per floor — long sightlines down the corridor, compartmented
  apartments. **Conclude:** visibility structure mirrors the public/private gradient.

## 22. Visibility heatmap (VGA)
- Per-floor visual-integration heatmap; corridor + open podium score highest.

## [NEW] 22b. Multilevel spatial intelligence (the 3D building graph)
- **Show:** the all-levels 3D graph — **Ground · Podium · 3rd · 4th** stacked at true elevations
  (0, 5, 14, 18 m), the four cores drawn as the vertical red links joining them; same metrics
  (MST, shortest path, closeness, betweenness, communities) now computed on the **single
  building-wide graph** instead of per floor.
- **Why it matters (the headline of the update):** per-floor analysis answers "how does *this*
  plate work"; the **connected multilevel graph answers "how does the *tower* work"** — and only
  here does the role of the cores as the building's backbone become measurable end-to-end.
- **Conclude:** betweenness on the multilevel graph collapses almost entirely onto the cores,
  confirming them as the structural *and* circulatory spine.

---

# SECTION 05 — NODE CLASSIFICATION (can a GNN name the rooms?)

> Run on the **semantic room graph**. Train a GNN to predict each room's type from its features
> + its neighbours, then test on held-out rooms.

## [NEW] 25b. The task
- **Input:** room graph (node = room, edge = door). **Node features:** zoning (4-d) +
  connectivity (3-d) — deliberately *coarse* (they don't reveal the exact type). **Target:** the
  9-class room type. **Model:** GraphSAGE (PyTorch-Geometric via topologicpy).
- **Why it matters / what we want to prove:** because the features are coarse, the model *cannot*
  read the answer off a node — it must use **graph structure** (a room surrounded by bedrooms and
  reached only through a corridor is probably a bathroom). Success = proof that **layout topology
  carries room-function information**.

## 28. Training & results
- **Show:** training/validation loss curve + the room-type **confusion matrix** + test metrics.
- **Floor 04 (single floor):** 127 rooms, ~130 door edges → **test accuracy ≈ 0.91, overall
  ≈ 0.94**, F1 ≈ 0.87.
- **Conclude:** a small GNN learns to name rooms from structure at ~90% — the topology hypothesis
  holds.

## [NEW] 28b. Where it fails (and why that's interesting)
- The one systematic confusion is **storeroom ↔ bathroom**. They share *identical* zoning +
  connectivity features, so only graph position separates them — and small service rooms sit in
  near-identical positions.
- **Say:** the error is *honest and architectural*, not random — the model fails exactly where a
  human with only the graph (no plan) would also hesitate. That's a feature, not a bug.

## [NEW] 28c. Multilevel node classification (F03 + F04)
- **Show:** the two floors joined through the stairs/core into one 228-room graph; true-vs-
  predicted plan panels per floor (mis-classified rooms ringed in red).
- **228 rooms, 235 edges (incl. cross-floor stair links) → per-floor accuracy ≈ 92–93%.**
- **What we prove:** the approach scales across floors and the **cores carry the graph between
  levels**, so the classifier sees one continuous building, not isolated plates.

## [NEW] 28d. Reproducible outputs
- Every run writes to `Exports/Prediction_F04/` (and `Exports/Prediction_MultiFloor/`): `nodes.csv`, `edges.csv`,
  `graphs.csv`, `node_predictions.csv`, plus an auto-generated `analysis_summary.md`,
  `analysis_metadata.json` and a one-page `15_summary.png` (distribution + confusion matrix).
- **Say:** the pipeline is push-button and audit-able — re-runnable on any future floor.

## 29–31. Unit typology & floor plans (context for the test set)
- Duplex / single-floor / triplex units stitched into one graph via 4 cores + per-unit stairs;
  GF, F1, F2, Rooftop plans coloured by apartment.
- **Say:** this is the architectural ground truth behind the node labels.

---

# SECTION 06 — LIMITATIONS & IMPROVEMENTS  **[to be built — content ready]**

## L1. What we proved
1. A real Gulf high-rise **can be encoded** in the MSD graph standard (nodes/edges/CSV).
2. **Spatial-intelligence metrics** recover the building's circulation logic — cores dominate
   betweenness, the spine dominates integration, communities ≈ apartment units.
3. A **GNN predicts room type at ~90%** from coarse features + topology — layout structure
   carries function.
4. The method **scales to a connected multilevel graph** through the cores.

## L2. Limitations (be honest)
- **Small test set:** one tower, ~120–230 rooms → accuracy is indicative, high variance per run.
  *(Mitigation: `pyg.CrossValidate(k_folds=5)` for a stable estimate.)*
- **Coarse features:** zoning + connectivity only; no area, proportion, window count, or
  orientation in the model yet → service rooms (storeroom/bathroom) are ambiguous.
- **Door-derived edges:** balcony/exterior access depends on doors being on the aperture layer;
  an adjacency fallback patches isolated rooms but is geometric, not designed.
- **No pretrained MSD weights used:** we train our own classifier on the tower; we have not yet
  validated against the full 5,372-plan MSD benchmark.
- **Hand-labelling:** ground truth = our own room classification → human error propagates.

## L3. Improvements (next steps)
- Add **geometric node features** (area, aspect ratio, #windows, perimeter) — should resolve the
  storeroom/bathroom confusion.
- **Cross-validate** + report mean ± std; **train on MSD, predict on Kifaf** (true inductive test).
- Extend the multilevel graph to **more floors** (the residential stack repeats) and the real
  **4-core / per-unit-stair** topology instead of auto-placed connectors.
- Couple the two analyses: use **spatial-intelligence metrics (betweenness, integration) as extra
  node features** for the classifier.

## L4. Why this matters (close the loop)
- A building encoded as a graph is **queryable, comparable, and learnable**: design QA (is this
  bathroom plausibly placed?), early-stage auto-labelling of BIM, and benchmarking a real tower
  against thousands of precedents — all from the same representation.

---

### Appendix — figure → source map (for assembling the deck)
| Slide | Figure | Comes from |
|---|---|---|
| 13, 15–22 | per-floor grid graph + metrics | `DubaiTower_MultiFloor_Spatial_Intelligence` / per-floor `FP 03x`,`FP 04x` |
| 22b | 3D multilevel building graph + metrics | `DubaiTower_AllLevels_Spatial_Intelligence` |
| 26–27 | 3D room cells + room graph | `DubaiTower_F04_Semantic_Prediction` (§4, §6) |
| 28, 28b, 28d | loss curve, confusion matrix, summary png | `DubaiTower_F04_Semantic_Prediction` (§10–15) |
| 28c | multilevel true-vs-pred panels | `DubaiTower_MultiFloor_Semantic_Prediction` |
| 19 (areas) | community area table | spatial notebooks, §18/§22 export |
