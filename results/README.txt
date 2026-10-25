Result files of the paper (experiment exp_3f32d3: diffuse-scattering labels, 3e9 rays per transmitter).
All numbers, tables and figures of the letter are computed from these files.

results.jsonl          one line per trained model (198 runs): accuracy, cost, settings, timings
permap.csv             per-test-map squared errors of every run (used for the bootstrap)
tuning_log.csv         every tuning trial (6 per model)
hp_7GHz_<model>.json   selected hyperparameters and tuning time
coverage_eval.jsonl    coverage-hole F1, precision, recall and AUROC (140/146/150 dB)
ksuff_7GHz.json        check that 1500 pixels per map are enough for the classical learners
incity_eval.jsonl      in-city control (Cell 17), if present
config_snapshot.json   configuration, fingerprints and package versions
paper/                 tables and figures written by the analysis cells (paper/covered_pixels: covered-pixel versions)

The same files are included in UrbanRM7_metadata_results.zip of the v1.0 release.
