# GraphML — Rania Chihaoui

Spatial graph analysis notebooks covering evacuation path analysis (IFC), residential OBJ-based topology, and stadium bowl egress.

---

## Environment Setup

### Python Version

**Python 3.11 is required.** Do not use 3.12 or newer — `topologic_core` ships compiled C++ binaries that are only built for 3.11.

Check your installed version:
```powershell
py -3.11 --version
```

If Python 3.11 is not installed, download it from [python.org](https://www.python.org/downloads/release/python-3114/).

---

### Create and Activate the Virtual Environment

```powershell
py -3.11 -m venv .gmlenv
.gmlenv\Scripts\Activate.ps1
```

> If PowerShell blocks script execution, run once as admin:
> `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

---

### Install All Packages

```powershell
pip install --upgrade pip

# Core topology engine
pip install topologicpy

# Visualization
pip install plotly kaleido

# Graph / ML
pip install networkx igraph

# Jupyter
pip install jupyterlab ipywidgets ipykernel

# PyTorch (CPU-only) + PyG — for GNN notebooks only
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
pip install torch_geometric
```

> `topologicpy` automatically pulls in `topologic_core`, `numpy`, `scipy`, `pandas`, `lark`, and other dependencies.

---

### Verified Installed Versions (as of 2026-07)

| Package | Version |
|---|---|
| Python | 3.11.4 |
| topologicpy | 0.9.43 |
| topologic_core | 8.0.4 |
| plotly | 6.7.0 |
| kaleido | 1.3.0 |
| networkx | 3.6.1 |
| igraph | 1.0.0 |
| numpy | 2.4.4 |
| scipy | 1.17.1 |
| pandas | 3.0.2 |
| lark | 1.3.1 |
| jupyterlab | 4.5.7 |
| ipywidgets | 8.1.8 |
| ipykernel | 7.2.0 |
| torch | 2.12.0+cpu |
| torch_geometric | 2.7.0 |

---

## IFC Analysis — What You Need

The notebook `IFC_Evacuation_Analysis_RaniaChihaoui.ipynb` uses **topologicpy's built-in IFC parser** (`IFCFastTopology`). You do **not** need `ifcopenshell`.

### Required packages for IFC notebooks

```powershell
pip install topologicpy plotly
```

That is all. Both are satisfied by the full install above.

### What `topologicpy` provides for IFC

| Class used | What it does |
|---|---|
| `IFC.FileByPath` | Parses the `.ifc` file (built-in parser, ~3 s for 273 k entities) |
| `IFC.TopologiesByPath` | Extracts `IfcSpace` / `IfcDoor` geometry + dictionaries |
| `IFC.AccessGraph` | Builds a centroid graph of spaces connected through door connectors |
| `IFC.DoorSpaceCardinalityReport` | Identifies exterior exit doors (space_count == 1) |
| `Graph.ShortestPath` | Dijkstra shortest path between two vertices |
| `Wire.Straighten` | Removes staircase artefacts from grid-sampled paths |
| `Wire.Length` | Euclidean path length in model units (metres) |

### IFC file requirements

- Revit/Archicad exports give each wall **real thickness** — this means adjacent room footprints never share a coincident edge, so the `Shell.ByFaces` grid-slice method from the reference notebooks will fail. The IFC notebook works around this using `IFC.AccessGraph` (door-centroid graph) instead.
- The IFC file must contain `IfcSpace` and `IfcDoor` entities for egress analysis. `IfcStair` geometry is not traced — stair travel distance is approximated as `2 × floor-to-floor elevation`.

---

## OBJ / Stadium Bowl Analysis — What You Need

For the OBJ-based pipeline (`GraphML_RaniaChihaoui.ipynb`, Dubai Tower notebooks, and the upcoming stadium bowl notebook):

```powershell
pip install topologicpy plotly kaleido igraph
```

### OBJ axis gotcha

`Topology.ByOBJPath` converts OBJ Y-up → topologic Z-up. If an imported model lands with the plan in X–Z (Y constant) instead of X–Y, rotate the OBJ in Rhino before export, or adjust the axis parameters in the notebook.

### Rhino export requirements (for stadium bowl)

Each layer must be exported as its own `.obj` file:

| Layer in Rhino | Export as |
|---|---|
| Stand_A | `assets/OBJ/Stand_A.obj` |
| Stand_B | `assets/OBJ/Stand_B.obj` |
| Stand_C | `assets/OBJ/Stand_C.obj` |
| Stand_Corner | `assets/OBJ/Stand_Corner.obj` |
| Concourse | `assets/OBJ/Concourse.obj` |
| Vomitorio_A | `assets/OBJ/Vomitorio_A.obj` |
| Vomitorio_B | `assets/OBJ/Vomitorio_B.obj` |
| Vomitorio_C | `assets/OBJ/Vomitorio_C.obj` |
| Vomitorios_Corners | `assets/OBJ/Vomitorios_Corners.obj` |
| Exit_Boundary | `assets/OBJ/Exit_Boundary.obj` |

**All stand/concourse Breps must be closed solids** (`SelClosedPolysrf` in Rhino). Vomitorio surfaces must be exactly coincident with the shared face at the stand–concourse interface (tolerance 0.001 for `CellComplex.ByFaces`, tolerance 0.5 for `Topology.AddApertures`).

---

## Running Jupyter

```powershell
.gmlenv\Scripts\Activate.ps1
jupyter lab
```

Or open directly in VS Code with the `.gmlenv` kernel selected.

---

## Known Issues

- **`kaleido` required for PNG export.** Without it, `fig.write_image(...)` raises an error. `fig.show()` (interactive HTML) works without kaleido.
- **`igraph` required for community detection.** `Graph.CommunityPartition` in topologicpy calls igraph internally. All other graph sections run without it.
- **`Wire.IsManifold` warnings** during `Wire.Straighten` are harmless — they appear when a path is a single edge (start room is directly adjacent to exit) and can be ignored.
- **IFC model connectivity gaps.** `IFC.AccessGraph` only connects rooms through explicitly modeled door connectors. Rooms with no modeled door in the IFC will appear as isolated nodes. This is a property of the source file, not a bug.
