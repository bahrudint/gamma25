# 📘 Notebook 1 — Exploring Raw Electron Tracks

**Audience**: Senior undergraduates with Python experience  
**Goal**: Get familiar with electron track data: structure, scale, and visual appearance  
**Scope**: Load raw tracks, visualize them in 3D, explore how energy and topology relate

---

## 🧠 Background

Each track in this dataset is a result of simulating an electron traveling through a Time Projection Chamber (TPC).  
You're given:
- 3D spatial information of charge deposition (`r`)
- Associated charge at each point (`num_e`)
- Ground truth origin and direction (from `.pickle` metadata)
- A `Tracks` object to abstract loading and processing

We will **explore raw tracks**, without drift simulation or readout applied.

---

## 📂 Directory Overview

```

example\_data/
└── electrons/
├── E0000100/   ← 100 keV
├── E0001000/   ← 1 MeV
└── E0050000/   ← 5 MeV

````

Each subfolder contains:
- `.npz` files with compressed or raw track data
- `.pickle` files with truth metadata

---

## ⚙️ Setup

```python
import os
import glob
import pickle
import numpy as np
import matplotlib.pyplot as plt
from Gampy.tools import tracks_tools

# Choose an electron track at 1 MeV
folder = 'example_data/electrons/E0001000'
filename = glob.glob(os.path.join(folder, 'TrackE*.npz'))[0]
````

---

## 📦 Load the Track

```python
# Extract energy from filename
energy = int(filename.split("TrackE")[1].split("_")[0])

# Load track data
track = tracks_tools.Tracks(filename.rstrip('.npz'))

# Load ground truth
pkl_name = filename.replace('.npz', '.pickle').replace('.c.pickle', '.pickle')
with open(pkl_name, 'rb') as f:
    data = pickle.load(f)['truth']
    origin = data['origin']
    direction = data['initial_direction']
    num_electrons = data['num_electrons']
    first_interaction = data.get('first_interaction', 'compton')
    if first_interaction not in ['pair', 'compton']:
        first_interaction = 'compton'
```

---

## 🔍 Inspect the Track Data

```python
positions = track.raw['r']         # shape (3, N)
charges = track.raw['num_e']       # shape (N,)
positions = positions.T            # shape (N, 3)

print(f"Track energy: {energy/1000:.1f} MeV")
print(f"Number of points: {positions.shape[0]}")
print(f"Total charge: {charges.sum():.1f} e⁻")
```

---

## 🧭 3D Visualization

```python
fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot(111, projection='3d')

sc = ax.scatter(positions[:,0], positions[:,1], positions[:,2],
                c=charges, cmap='plasma', s=2, alpha=0.8)

ax.set_title(f"{energy/1000:.1f} MeV Electron Track — Raw")
ax.set_xlabel("X (m)"); ax.set_ylabel("Y (m)"); ax.set_zlabel("Z (m)")
plt.colorbar(sc, label="Charge (e⁻)")
plt.show()
```

---

## 🧠 Challenge 1 — Total Charge

**Task**:
Calculate the total charge in this track and compare it with the `num_electrons` value from the pickle.

```python
total_from_data = charges.sum()
print(f"From track: {total_from_data:.1f} e⁻")
print(f"From truth: {num_electrons:.1f} e⁻")
```

* Are they close? Why might they differ slightly?

---

## 🧠 Challenge 2 — Shape vs Energy

Explore how the shape of tracks changes with energy:

```python
energies = [100, 1000, 5000]
folders = [f"example_data/electrons/E{e:07d}" for e in energies]

fig = plt.figure(figsize=(15, 5))

for i, folder in enumerate(folders):
    file = glob.glob(os.path.join(folder, 'TrackE*.npz'))[0]
    track = tracks_tools.Tracks(file.rstrip('.npz'))
    pos = track.raw['r'].T
    ch = track.raw['num_e']
    
    ax = fig.add_subplot(1, 3, i+1, projection='3d')
    ax.scatter(pos[:,0], pos[:,1], pos[:,2], c=ch, cmap='inferno', s=1)
    ax.set_title(f"{energies[i]/1000:.1f} MeV")
    ax.set_xlabel("X"); ax.set_ylabel("Y"); ax.set_zlabel("Z")

plt.suptitle("Raw Electron Tracks at Different Energies")
plt.tight_layout()
plt.show()
```

* What trends do you observe?

  * Do higher-energy tracks have more scatter?
  * How does the track length or spread change?

---

## 🧠 Challenge 3 — Bounding Box & Spatial Extent

Measure the physical size of a track.

```python
extent = positions.max(axis=0) - positions.min(axis=0)
print(f"Track extent (X, Y, Z) in meters: {extent}")
```

* Which axis has the greatest extent? Why might that be?

---

## 📝 Wrap-Up

By now you should:

* Know how to open and inspect a track
* Understand what the `track.raw` data structure provides
* Visualize tracks in 3D with charge color-mapping
* Appreciate how track topology scales with energy

---

## 🚀 Next Step

In the next notebook, you'll:

* Cluster raw data to isolate primary tracks
* Identify and locate the **track head**
* Prepare tracks for drift simulation and ML input

```
