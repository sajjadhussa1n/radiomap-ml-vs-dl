# To Deep Learn or Not: Accuracy, Data and Cost of Radio Map Prediction in the Upper Mid-Band

Code for the IEEE Antennas and Wireless Propagation Letters paper of the same title (S. Hussain, NUST).

The study compares five classical learners (ridge regression, k-NN, SVR, random forest, LightGBM), a per-pixel MLP and three spatial networks (UNet-S, UNet-M, Swin-UNet). All models receive the same 17-channel per-pixel feature stack, are trained on 32 to 1600 ray-traced path-loss maps at 7 GHz from ten cities, and are tested on three unseen cities.

## Contents

| Path | Description |
|---|---|
| `ML_vs_DL_radio_maps.ipynb` | Complete pipeline: OpenStreetMap scenes, Sionna RT ray tracing, feature extraction, tuning and training of all nine models, and every analysis in the paper |
| `results/` | Result files produced by the notebook, from which all numbers in the paper are computed |

## Running the notebook

1. Open the notebook in Google Colab and select a GPU runtime (the paper used a High-RAM runtime with an NVIDIA T4).
2. Optionally set the environment variable `RM_DRIVE_ROOT` to choose the Google Drive folder for outputs (default `/content/drive/MyDrive/rm_ml_vs_dl`).
3. For a quick test, set `DRY_RUN = True` in the configuration cell. The whole pipeline then runs on procedural cities on a CPU in a few minutes.
4. For the full study, keep `DRY_RUN = False` and run all cells. All phases are resumable.

Main dependencies: `sionna-rt` 2.2, PyTorch, scikit-learn 1.6, LightGBM 4.6, osmnx 2.1, shapely, trimesh. The first cell installs any that are missing.

## Results files

The `results/` folder contains the logs written by the notebook to `exp/exp_de1281/` on Google Drive:

- `results.jsonl` (one line per trained model: accuracy, cost, settings),
- `permap.csv` (per-test-map errors used for the bootstrap),
- `coverage_eval.jsonl` (coverage-hole F1 and AUROC),
- `hp_7GHz_*.json` (selected hyperparameters),
- `config_snapshot.json` (configuration, fingerprints and package versions),
- the tables written to `paper/` and `paper/covered_pixels/`.

## Data

Building footprints are downloaded from OpenStreetMap through the Overpass API. Map data © OpenStreetMap contributors, available under the Open Database License (ODbL). Derived scenes or ray-traced maps that are shared must keep this attribution.

## Citation

If you use this code, please cite the paper (citation details will be added after publication).

## License

MIT License (see `LICENSE`).
