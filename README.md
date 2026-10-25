# UrbanRM7: Ray-Traced Radio Maps at 7 GHz, and *To Deep Learn or Not*

This repository provides the **UrbanRM7 dataset** and the code of the IEEE Antennas and Wireless Propagation Letters paper
*To Deep Learn or Not: Accuracy, Data and Cost of Radio Map Prediction in the Upper Mid-Band* (S. Hussain, NUST).

## The UrbanRM7 dataset

| | |
|---|---|
| Maps | 2400 path-loss maps (150 tiles × 16 rooftop transmitters) |
| Area and grid | 256 m × 256 m tiles, 1 m pixels, receiver plane 1.5 m above ground |
| Cities | 15 cities from OpenStreetMap, split by city: 10 for training (1600 maps), 2 for validation (320), 3 for testing (480) |
| Frequency | 7 GHz (FR3 / upper mid-band) |
| Ray tracer | Sionna RT `RadioMapSolver`: specular reflection, wedge and rooftop diffraction, diffuse scattering (S = 0.3), up to 5 interactions, 3×10⁹ rays per transmitter |
| Materials | ITU concrete (buildings) and ITU medium-dry ground |
| Label noise | σ ≈ 2.1 dB, measured with a second Monte-Carlo seed for 160 maps |
| Features | 17 geometric channels per pixel: building height and mask, building fraction within 10/25/50 m, distance to the nearest building, street-canyon flag, Tx height, log 2-D and 3-D distance, elevation, LoS flag, buildings crossed, depth inside buildings, tallest building on the path, obstacle excess height, knife-edge parameter |
| Also included | Sionna scenes (XML + PLY meshes) for every tile, LoS-only and repeated-seed check runs, and every result file of the paper |

### Download

The data are published as assets of the [GitHub release v1.0](https://github.com/sajjadhussa1n/radiomap-ml-vs-dl/releases/tag/v1.0):

| File | Content |
|---|---|
| `UrbanRM7_metadata_results.zip` | metadata, licence, checksums, RT check runs and the paper results (small) |
| `UrbanRM7_train.zip` | 100 training tiles (1600 maps) |
| `UrbanRM7_val.zip` | 20 validation tiles (320 maps) |
| `UrbanRM7_test.zip` | 30 test tiles (480 maps) |
| `UrbanRM7_scenes.zip` | Sionna scenes of all 150 tiles |

Unzip everything into the same folder, or let the tutorial notebook download the parts you need. A part larger than
2 GB is split into `_part1`, `_part2`, … files, which the notebook handles automatically.

### File format

```
UrbanRM7/
  metadata/tiles.csv        city, split, centre latitude/longitude, building cover and heights of every tile
  metadata/maps.csv         tile, transmitter index and position, floor share and RT time of every map
  metadata/channels.csv     name, unit and description of the 17 feature channels
  metadata/config.json      ray-tracing settings, materials, grid convention, label policy
  data/{train,val,test}/<scene_id>.npz
  scenes/<scene_id>/scene.xml, meshes/*.ply
  checks/los_<scene_id>.npz, checks/noise_<scene_id>.npz
  results/
  SHA256SUMS.txt, LICENSE.md, README.md
```

Each tile file (`numpy.load`) contains

| Key | Shape, type | Content |
|---|---|---|
| `pathloss_db` | 16 × 256 × 256, float32 | raw path loss in dB, NaN where no path reaches the pixel |
| `reached` | 16 × 256 × 256, bool | outdoor pixel reached by at least one path |
| `outdoor` | 256 × 256, bool | pixel outside building footprints |
| `height_m` / `height_fine_m` | 256 × 256 / 512 × 512, float32 | building height raster at 1 m / 0.5 m |
| `features_scene` | 7 × 256 × 256, float16 | transmitter-independent channels |
| `features_tx` | 16 × 10 × 256 × 256, float16 | transmitter-dependent channels |
| `tx_xyz_m` | 16 × 3, float32 | transmitter position in tile coordinates (m) |
| `rt_seconds` | 16, float32 | ray-tracing time per map on an NVIDIA T4 |

Row *i* corresponds to *y* and column *j* to *x*, both increasing, with the origin at the tile centre. The paper uses the
label policy *path loss clipped at 160 dB, unreached outdoor pixels set to 160 dB*; the raw values are kept so that
other policies can be applied.

### Getting started

[`UrbanRM7_tutorial.ipynb`](UrbanRM7_tutorial.ipynb) (Colab-ready) downloads the data, reads tiles and maps, plots
examples and dataset statistics, trains LightGBM on a selectable subset of the features and a small U-Net on the same
features, and compares their errors per city and per LoS/NLoS condition.

## Reproducing the paper

| Path | Description |
|---|---|
| `ML_vs_DL_radio_maps.ipynb` | Complete pipeline: OpenStreetMap scenes, Sionna RT ray tracing, feature extraction, tuning and training of all nine models, every analysis of the paper, the in-city control and the dataset export |
| `UrbanRM7_tutorial.ipynb` | Dataset access and model-training tutorial |

1. Open `ML_vs_DL_radio_maps.ipynb` in Google Colab with a GPU runtime (the paper used a High-RAM runtime with an NVIDIA T4).
2. Optionally set `RM_DRIVE_ROOT` to choose the Google Drive folder (default `/content/drive/MyDrive/rm_ml_vs_dl`).
3. `DRY_RUN = True` runs the whole pipeline on procedural cities on a CPU in a few minutes.
4. With `DRY_RUN = False` all phases run in full and can be resumed after an interruption. The experiment grid can be
   split over parallel Colab sessions with `SHARD` and `RUN_MODELS` in the configuration cell.

Main dependencies: `sionna-rt` 2.2, PyTorch, scikit-learn, LightGBM, osmnx, shapely, trimesh. The first cell installs
any that are missing. The tutorial needs only NumPy, pandas, Matplotlib, LightGBM and PyTorch.

## Licence

* Code: MIT License (`LICENSE`).
* Dataset: Open Database License (ODbL) 1.0 (`DATA_LICENSE.md`). The building geometry comes from OpenStreetMap,
  © OpenStreetMap contributors; derived databases that are shared must keep this attribution and licence.

## Citation

If you use the dataset or the code, please cite the paper (see `CITATION.cff`; full details will be added after publication).
