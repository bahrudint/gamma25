# 📘 Notebook 1 — Exploring Raw Electron Tracks

**Goal**: Get familiar with electron track data: structure, scale, and visual appearance  
**Scope**: Load raw tracks, visualize them in 3D, explore how energy and topology relate

---

## 🧠 Background

Each track represents an electron propagating through a Time Projection Chamber (TPC), simulated in liquid argon.  
We capture:
- 3D charge deposition (`r`)
- Charge intensity at each point (`num_e`)
- Ground truth properties from associated metadata files

This notebook helps you explore the raw data and build an intuition for track shapes and behavior.

---

## 📂 Data Structure

```

example_data/
└── electrons/
├── E0000100/   ← 100 keV
├── E0001000/   ← 1 MeV
└── E0050000/   ← 5 MeV
...

````

Each folder contains:
- `.npz` files: track data
- `.pickle` files: truth metadata (origin, direction, interaction type)

---

## 🧰 Setup and Imports

```python
import os
import glob
import pickle
import numpy as np
import matplotlib.pyplot as plt
from Gampy.tools import tracks_tools
````

---

## 📦 Load a Track

```python
# Choose one electron track file
folder = 'example_data/electrons/E0001000'
track_file = glob.glob(os.path.join(folder, 'TrackE*.npz'))[0]

# Extract energy from filename (in keV)
energy = int(track_file.split("TrackE")[1].split("_")[0])

# Initialize the Track object
track = tracks_tools.Tracks(track_file.rstrip('.npz'))

# Load associated truth data
truth_file = track_file.replace('.npz', '.pickle').replace('.c.pickle', '.pickle')
with open(truth_file, 'rb') as f:
    truth = pickle.load(f)['truth']
```

---

## 🔎 Examine Track Properties

```python
positions = track.raw['r'].T                # shape: (N, 3)
charges = track.raw['num_e']                # shape: (N,)

print(f"Track energy:           {energy / 1000:.1f} MeV")
print(f"Number of points:       {len(positions)}")
print(f"Total deposited charge: {charges.sum():.1f} e⁻")

# Optionally examine truth
print(f"True origin:            {truth['origin']}")
print(f"Initial direction:      {truth['initial_direction']}")
```

---

## 🎨 Visualize the Track in 3D

```python
fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot(111, projection='3d')

sc = ax.scatter(positions[:, 0], positions[:, 1], positions[:, 2],
                c=charges, cmap='plasma', s=2, alpha=0.8)

ax.set_title(f"{energy / 1000:.1f} MeV Electron Track — Raw")
ax.set_xlabel("X (m)")
ax.set_ylabel("Y (m)")
ax.set_zlabel("Z (m)")

plt.colorbar(sc, label="Charge (e⁻)")
plt.tight_layout()
plt.show()
```

---

## 🧠 Challenge 1 — Compare Charge

```python
total_charge_data = charges.sum()
total_charge_truth = truth['num_electrons']

print(f"Track-integrated charge: {total_charge_data:.1f} e⁻")
print(f"Truth label charge:      {total_charge_truth:.1f} e⁻")
```

* Are the values close? Why might there be a discrepancy?

---

## 🧠 Challenge 2 — Energy Scaling

**Visualize tracks at different energies to compare spread and structure.**

```python
energies = [100, 1000, 5000]  # keV
folders = [f"example_data/electrons/E{e:07d}" for e in energies]
files = [glob.glob(os.path.join(folder, 'TrackE*.npz'))[0] for folder in folders]

fig = plt.figure(figsize=(16, 5))

for i, file in enumerate(files):
    tr = tracks_tools.Tracks(file.rstrip('.npz'))
    pos = tr.raw['r'].T
    ch = tr.raw['num_e']

    ax = fig.add_subplot(1, 3, i + 1, projection='3d')
    ax.scatter(pos[:, 0], pos[:, 1], pos[:, 2], c=ch, cmap='inferno', s=1)
    ax.set_title(f"{energies[i] / 1000:.1f} MeV")
    ax.set_xlabel("X"); ax.set_ylabel("Y"); ax.set_zlabel("Z")
    ax.grid(True)

plt.suptitle("Raw Electron Tracks at Different Energies", fontsize=14)
plt.tight_layout()
plt.show()
```

* How does the spatial extent change?
* Do higher energies result in longer or more branched tracks?

---

## 🧠 Challenge 3 — Track Extent

**Compute the physical size of the track.**

```python
extent = positions.max(axis=0) - positions.min(axis=0)
print(f"Track extent (X, Y, Z): {extent} meters")
```

* Which direction has the most spread?
* Why is that relevant for direction-finding tasks?

---

## ✅ Summary

You have:

* Loaded and explored real simulated tracks from electron interactions
* Visualized 3D charge distributions
* Observed energy-dependent changes in track topology

You now have a basic grasp of:

* What an electron track looks like
* How charge is spatially deposited
* What truth data accompanies each track


