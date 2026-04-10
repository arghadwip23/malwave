# MalWave — Android Malware Detection with Temporal Drift Analysis

> A corrected and extended signal processing pipeline for Android malware detection,
> introducing the **Fingerprint Age Index (FAI)** — a novel metric for quantifying
> how ML model accuracy degrades as the malware threat landscape evolves over time.

---

## Overview

Most Android malware detection models are trained and evaluated on static datasets,
ignoring a fundamental problem: **malware evolves**. A model trained on 2021 APKs
will perform significantly worse by 2024 — not because the model is wrong, but because
the behavioral fingerprints it learned have become stale.

MalWave addresses this by building a **temporal drift analysis framework** on top of
signal-processing-based malware detection. Using timestamped APKs from
[AndroZoo](https://androzoo.uni.lu/), the pipeline:

- Extracts API call sequences from Android APKs
- Converts sequences into signal representations using a corrected processing chain
- Trains detection models on time-stratified dataset splits
- Measures concept drift over time using the **Fingerprint Age Index (FAI)**

This work extends and corrects the original MalWave implementation, addressing
**six identified flaws** in the prior signal processing pipeline including windowing
artifacts, normalization drift, and temporal feature leakage.

---

## Key Contribution: Fingerprint Age Index (FAI)

The FAI quantifies how "stale" a model's learned behavioral fingerprints become
as the malware ecosystem shifts. Concretely, it measures the rate of degradation
in detection confidence across time-stratified evaluation windows, enabling
researchers to:

- Predict when a deployed model needs retraining
- Compare the temporal robustness of different feature representations
- Build datasets that reflect real-world deployment conditions

---

## Pipeline Stages

```
AndroZoo APKs (timestamped)
        │
        ▼
  APK Preprocessing
  (manifest parsing, permission extraction)
        │
        ▼
  API Call Sequence Extraction
        │
        ▼
  Signal Representation
  (corrected windowing, normalization, feature extraction)
        │
        ▼
  Time-Stratified Dataset Construction
  (train/test splits respect temporal ordering)
        │
        ▼
  Model Training & Evaluation
        │
        ▼
  FAI Computation & Drift Analysis
```

---

## Corrections Over Original MalWave

The original MalWave signal processing implementation contained several issues that
inflated reported detection accuracy. This pipeline corrects:

1. **Windowing artifacts** — improper window boundaries causing signal discontinuities
2. **Normalization drift** — test-set statistics leaking into normalization parameters
3. **Temporal feature leakage** — future APK behavior influencing past-window features
4. **Inconsistent resampling** — variable-length sequences not normalized correctly
5. **Label alignment errors** — timestamp mismatches between APK metadata and labels
6. **Split contamination** — benign/malware class imbalance not preserved across time splits

---

## Dataset

APKs are sourced from [AndroZoo](https://androzoo.uni.lu/), a large repository of
Android applications with associated metadata including upload timestamps, VirusTotal
scan results, and SHA256 hashes.

To use this pipeline you will need:
- An AndroZoo API key (free for academic use — [request here](https://androzoo.uni.lu/access))
- Python 3.9+
- The dependencies listed in `requirements.txt`

---

## Getting Started

```bash
git clone https://github.com/arghadwip23/malwave.git
cd malwave
pip install -r requirements.txt
```

Then open `malwav.ipynb` in Jupyter and follow the cells in order. Each stage
of the pipeline is documented inline.

> **Note:** You will need to configure your AndroZoo API key and set the APK
> download path before running the data collection cells.

---

## Dependencies

- `androguard` — APK static analysis
- `numpy`, `scipy` — signal processing
- `scikit-learn` — model training and evaluation
- `pandas` — dataset management
- `matplotlib`, `seaborn` — visualization

---

## Status

This project is under **active development** as part of an ongoing academic
conference submission. The core pipeline and FAI metric are implemented;
additional experiments and documentation are in progress.

- [x] Corrected signal processing chain
- [x] Time-stratified dataset construction
- [x] FAI metric implementation
- [ ] Full experimental results
- [ ] Pretrained model weights
- [ ] CLI interface for standalone use

---

## Citation

If you use this pipeline or the FAI metric in your research, please cite this
repository (formal citation will be updated upon paper publication):

```
@misc{malwave2026,
  author       = {Sarkar, Arghadwip},
  title        = {MalWave: Android Malware Detection with Temporal Drift Analysis},
  year         = {2026},
  publisher    = {GitHub},
  url          = {https://github.com/arghadwip23/malwave}
}
```

---

## Contributing

This is a research project under active development. Issues and discussions are
welcome — particularly from researchers working on Android security, concept drift,
or signal-based malware analysis.

---

## License

MIT License — see [LICENSE](LICENSE) for details.
