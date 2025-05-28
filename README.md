# Electron Track Analysis 2025 — GammaTPC ML Study

Welcome to the central hub for our collaborative effort to study **electron tracks above 100 keV** in the **GammaTPC/GAMPix detector** using analytical methods and machine learning techniques.

This work builds on the concepts and detector technology described in:
- [GammaTPC Concept Paper](https://arxiv.org/abs/2502.14841) _skim this paper, look at images_
- [Probabilistic Deep Learning for Electron Tracks in LArTPC](https://arxiv.org/abs/2207.07805) _this one is important and it will be the basis of our study_
- [GAMPix Readout System](https://arxiv.org/abs/2402.00902) _this one explains track properties and our readout methods_ 

---

## 🔍 Purpose

This repository supports a focused research study in **Summer–Fall 2025**
Our aim is to improve reconstruction and understanding of **electron track head location** and **initial direction**, especially in the **Compton** and **Pair Production** regimes (> 0.1 MeV).

### Primary Objectives

- ✅ Machine Learning models for:
  - Head location (start of track)
  - Initial direction vector
- ✅ Separate studies for:
  - **Compton regime**
  - **Pair Production regime**

These will lead to the:
- ✅ Angular resolution and sensitivity estimation of GammaTPC
- ✅ 2-3 Papers 

### Bonus Explorations
- Polarization sensitivity
- Effects of drift length, readout pitch, and energy
- Scattering path reconstruction, energy estimation from that
- Reconstruction optimization (CKD, permutation handling)

---

## 📆 Timeline

| Period | Milestone |
|--------|-----------|
| June (Weeks 1–2) | Onboarding, setup, Hello-world notebooks |
| June (end) | Plan large-scale ML study |
| July–August | Model training, validation, and refinement |
| September | Final results + draft and submit 2 papers |
| Oct–Dec | Continued dev, optimization, bonus tasks |

---

## 🧠 Structure of This Repo

```bash
gamma25/
├── README.md               # You're here!
├── docs/                   # Project documentation, slide decks, diagrams
├── notebooks/              # Jupyter notebooks (visualization, modeling, etc.)
├── scripts/                # Helper scripts for data processing and experiments
├── models/                 # Trained models, architecture definitions
├── example_data/           # Track samples or links to datasets
```

### Setup with:
```bash
git clone https://github.com/bahrudint/gamma25.git
cd gamma25
pip install -U -r requirements.txt
```
and the run the below to install Gampy (make sure to be in the gamma25 folder)

```bash
git clone https://github.com/tashutt/Gampy.git
cd Gampy
pip install -e .
```

## 🧪 Getting Started

If you're new to this repo, begin here:
```bash
    notebooks/01_intro.md — Load and visualize 3D tracks
    notebooks/02_analytic_head_direction.md — Analytical methods for head/direction
    notebooks/03_ml_baseline.md — Simple ML baseline for head prediction
```


## ✍️ Publications (Planned)
    📄 Electron Track Imaging at High Energy Using ML in GammaTPC
    📄 Sensitivity of GammaTPC in the Pair Production Regime
