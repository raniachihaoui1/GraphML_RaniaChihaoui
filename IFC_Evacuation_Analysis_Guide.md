# IFC Evacuation Analysis — Guide

**Notebook:** `IFC_Evacuation_Analysis_RaniaChihaoui.ipynb`
**Model:** `assets/NBU_MedicalClinic_IFC/NBU_MedicalClinic_Arch.ifc`

---

## What This Notebook Does

This notebook computes **egress distances** for every room in a multi-floor building — how far each occupant would need to walk to reach the nearest exit in an emergency. It produces:

- A ranked table of rooms by worst-case egress distance
- Floor-plan visualizations with every path color-coded green → yellow → red
- Identification of rooms with **no modeled egress path** (a code compliance flag)
- Worst-case, best-case, and average egress distances per floor and for the whole building

It does not simulate crowd dynamics or congestion — it is a **static geometric egress distance analysis**, which is the standard first step in fire safety and egress code review.

---

## Why This Approach (and Why Not the Other One)

### The standard approach: grid-slicing

The reference notebook from the course (`IFC_Path_Analysis.ipynb`) works by:
1. Merging all room floor faces into a single `Shell`
2. Slicing that shell with a fine grid to create a dense waypoint graph
3. Running shortest-path on that graph

**This fails on real Revit exports.** Revit gives walls real physical thickness, so neighboring room floor outlines have a gap between them — they do not share a coincident edge. `Shell.ByFaces` returns `None`, and even a `Cluster`-based workaround produces a fragmented graph where most rooms are unreachable from each other.

### This notebook's approach: door-centroid graph

Instead, this notebook uses `IFC.AccessGraph` — topologicpy's built-in method that:
1. Places a **vertex at each room centroid** (the `IfcSpace` geometry centroid)
2. Connects two vertices with an **edge wherever a door** (`IfcDoor` or `IfcSpace` connector) joins the two rooms

This mirrors exactly what the course's OBJ-based notebooks do with `CellComplex` apertures, but derived directly from the IFC's own semantic data rather than geometry. Every edge is a straight line from centroid to centroid through a real doorway — people never route through walls, which is what evacuation analysis requires.

The tradeoff: paths are centroid-to-centroid straight lines, not actual corridor geometry. Lengths represent door-to-door graph distances, not true walked distances bending around corners. This is acceptable for a comparative analysis (which room is worst, by how much) even if the absolute metres are approximate.

---

## Workflow Step by Step

### Section 1 — Load IFC and extract spaces & doors

```python
f = IFC.FileByPath(IFC_PATH)
spaces = IFC.TopologiesByPath(IFC_PATH, includeTypes=["ifcspace"], ...)
doors  = IFC.TopologiesByPath(IFC_PATH, includeTypes=["ifcdoor"],  ...)
```

`topologicpy`'s built-in IFC parser (`IFCFastTopology`) reads the file directly — no `ifcopenshell` needed. It returns `Topology` objects with attached `Dictionary` metadata (IFC id, name, level, area, etc.).

Every space is stored in `space_by_id` keyed by its IFC id string, with its centroid vertex pre-computed. This dictionary is the backbone of all later analysis.

---

### Section 2 — Identify exits and stairwells

**Exits** are found using `IFC.DoorSpaceCardinalityReport` — a report that counts, for each door, how many `IfcSpace` entities it borders:

| space_count | Meaning |
|---|---|
| 1 | The door touches only one interior space → the other side is outside → **exterior exit** |
| 2 | The door connects two rooms → interior door |

**Stairwells** are identified by matching `"STAIR"` in the room name/long name, then pairing First Floor ↔ Second Floor cores by their shared letter code (e.g. `1CS3` ↔ `2CS3`).

These two sets — `exit_room_ids` and `stair_pairs` — are the **destinations** for all routing.

---

### Section 3 — Grid demo on a single room (and why it doesn't scale)

This section is a **demonstration only** — it runs the grid-slice method from the reference notebook on the largest single room to show the technique works locally. It is not used in the building-wide analysis.

The output (`Wire.IsManifold` warnings aside) confirms: 593 waypoints, path found. Then it explains why running the same code on all 154 rooms fails — gaps between wall-thickness-padded footprints mean the merged shell can never be formed.

---

### Section 4 — Building-scale graph via `IFC.AccessGraph`

```python
g = IFC.AccessGraph(f, importMode="geometry", dictionaryMode="all", silent=True)
```

This single call builds the entire building graph. The result for this clinic:
- **269 vertices** (room centroids)
- **232 edges** (door connections)
- **48 connected components** — about half the building is unreachable from the other half through modeled doors alone

The 48 components are not a bug. Some isolation is correct by design (standalone utility rooms with their own exterior exit). Others reflect missing door objects in the Revit export (open-plan reception areas where no `IfcDoor` was placed). Both are reported explicitly as flagged rooms rather than silently dropped.

---

### Section 5 — Define sources and destinations

| | |
|---|---|
| **Sources** | Every `IfcSpace` on First Floor (154) and Second Floor (106) |
| **Destinations (FF)** | 10 exterior-exit rooms identified in Section 2 |
| **Destinations (SF)** | 3 stairwell cores on Second Floor (which connect down to First Floor) |

---

### Section 6 — Shortest paths, First Floor

For each First Floor room the `nearest_destination` function:

1. Looks up the room's vertex in the graph
2. Checks which component it belongs to
3. Iterates over all exit rooms **in the same component** (cross-component routing is geometrically impossible — there is no door connecting them)
4. Runs `Graph.ShortestPath` (Dijkstra) to each reachable exit
5. Returns the exit with the **smallest `Wire.Length`**

`Wire.Straighten` is applied to each path. Because the graph is already centroid-to-centroid straight segments (no grid staircase pattern to smooth), the straightened length equals the raw length here — unlike the Section 3 fine-grid demo where straightening meaningfully shortens paths.

Rooms with no reachable exit are collected in `ff_no_path` and flagged.

---

### Section 7 — Multi-floor analysis, Second Floor

Second Floor has no exterior exits of its own. The total egress distance for a Second Floor room is:

```
total = distance(room → nearest stairwell on SF)
      + stair_travel_distance          ← approximated as 2 × floor-to-floor height (≈ 9.14 m)
      + distance(matched stair on FF → nearest exit)
```

The stair travel distance is a simplification — actual flight geometry from `IfcStair` is not traced. This is noted as a limitation.

---

### Section 8 — Worst / average / best case

```
First Floor:   worst 49.4 m  ·  best 0.0 m  ·  avg 20.4 m
Second Floor:  worst 89.1 m  ·  best 11.4 m  ·  avg 54.3 m
Whole building: worst 89.1 m (room 2C18, via stairwell 1BS2)
```

Rooms with no modeled egress path: **77 total** (43 FF, 34 SF).

---

### Section 9 — Visualization

Three interactive Plotly figures are produced:

#### Figure 1 — Building overview
- Room footprints colored by type (gray = normal, green = exit, blue = stairwell, orange = no path)
- All graph edges shown as thin gray lines
- All graph nodes shown as small white circles — this is the **topological graph structure** overlaid on the floor plan

#### Figure 2 — First Floor egress
- All First Floor room footprints as background
- Full graph (gray edges + white node circles)
- Each room's egress path drawn as a **colored line with nodes** at each hop: green (short) → yellow → red (long)
- Worst-case room marked with a star and label
- Hover any path to see: room name, destination exit, distance in metres

#### Figure 3 — Second Floor egress
- Same structure as Figure 2 but for Second Floor
- Line color encodes **total combined distance** (including stair travel + FF leg), not just the visible leg to the stairwell
- A short visible line can still be colored red if that stairwell is itself far from an exit

---

## How to Adapt This to a Different Building

1. **Change `IFC_PATH`** at the top of the imports cell to point to your new IFC file.

2. **Re-run Sections 1–2.** Check the output — verify `spaces` and `doors` counts look reasonable, and that the level names match (the code uses `"First Floor"` / `"Second Floor"` by string match).

3. **Update level names** in Sections 5–7 if your building uses different strings (e.g. `"Ground Floor"`, `"Level 1"`).

4. **Stairwell naming** — the pairing logic strips the leading digit from the room name (`1CS3` → `CS3`) and matches it across floors. If your building uses a different naming convention, update the `stair_key()` function in Section 2.

5. **Stair travel distance** — update `STAIR_TRAVEL_DISTANCE` in Section 7 to `2 × your floor-to-floor height in metres`.

6. **Re-run Sections 4–9.** Everything downstream recomputes automatically.

---

## Key Limitations to Know

| Limitation | Impact |
|---|---|
| Centroid-to-centroid paths | Distances are approximate — do not bend through corridor geometry |
| IFC door connectivity gaps | Rooms without modeled `IfcDoor` objects appear unreachable even if physically connected |
| Stair geometry not traced | Stair leg uses `2 × floor height` approximation, not actual flight length |
| No congestion modelling | All occupants walk simultaneously at uniform speed with no queueing |
| No door width / capacity | Bottlenecks at narrow exits are not captured |

---

## Output Interpretation Cheat Sheet

| What you see | What it means |
|---|---|
| Green path, short | Room is close to an exit — low risk |
| Red path, long | Room is far — consider additional exits or signage |
| Orange room (overview) | No modeled egress at all — flag for review, likely missing door in IFC |
| Star marker | Worst-case room for that floor |
| Isolated green room | This room IS the exit (distance = 0) |
| Short SF line but red color | Room is close to a stairwell but that stairwell is far from an exterior exit |
