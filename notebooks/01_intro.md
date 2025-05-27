# === Notebook 1: Electron Tracks in TPCs: Intro & Basic Plotting ===

````markdown
# Electron Tracks in Time Projection Chambers (TPCs)

**Goal**:  
Introduce electron tracks, their representation, and basic visualization.

## What is an Electron Track?

An electron track is the path electrons take as they travel through a detector medium (e.g., Liquid Argon). These electrons leave ionization trails, allowing us to reconstruct particle interactions like Compton scattering or pair production.

**Key concepts**:
- Electron positions (3D)
- Charge (number of electrons at each position)
- Interaction type (Compton or Pair production)

---

## Loading & Exploring Track Data

Let's load some example data:

```python
import numpy as np

# Load example electron track
data = np.load("example_data/electrons/E0020000/TrackE0020000_00001.npz")
positions = data['r']  # (3, N)
charges = data['num_e']  # (N,)

print(f"Positions shape: {positions.shape}")
print(f"Charges shape: {charges.shape}")
````

* **Question**: What do you expect from the shapes printed above?

---

## Visualizing a Track (Basic)

Let's visualize the points in 3D space:

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(8,6))
ax = fig.add_subplot(111, projection='3d')

# Plot points
ax.scatter(positions[0], positions[1], positions[2], 
           c=charges, cmap='viridis', s=1)

ax.set_xlabel('X (m)')
ax.set_ylabel('Y (m)')
ax.set_zlabel('Z (m)')
ax.set_title('Electron Track: Position colored by charge')

plt.show()
```

* **Challenge**: Adjust the point size or alpha transparency to better visualize dense regions.

---

## Interactive Problem

**Problem**:

* Can you calculate the total number of electrons in the track?
* Where do you think the interaction started (head of track)?

```python
# Your code here:
total_electrons = ___

print(f"Total electrons: {total_electrons}")
```

**Hint**: Use `charges.sum()`.

---

# Summary

You've learned:

* What electron tracks represent
* How to load and visualize basic electron track data
* How to interpret the track visually

Next, you'll learn about clustering and identifying the track head!

````

---

# === Notebook 2: Clustering & Identifying the Electron Track Head ===

```markdown
# Electron Track Analysis: Clustering and Track Head Identification

**Goal**:  
Learn to use clustering (DBSCAN) to identify important track features.

---

## What is Clustering?

Clustering groups nearby points based on spatial proximity. We’ll use **DBSCAN** to find dense regions representing real electron interactions.

---

## Load and visualize your track first:

```python
import numpy as np
import matplotlib.pyplot as plt

data = np.load("example_data/electrons/E0020000/TrackE0020000_00001.npz")
positions = data['r'].T
charges = data['num_e']

fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.scatter(positions[:,0], positions[:,1], positions[:,2], s=1)
ax.set_title("Raw Electron Track")
plt.show()
````

---

## Apply DBSCAN clustering:

```python
from sklearn.cluster import DBSCAN

eps = 0.002
min_samples = 20

db = DBSCAN(eps=eps, min_samples=min_samples).fit(positions)
labels = db.labels_

print(f"Cluster labels: {set(labels)}")
```

* **Question**: How many clusters did you identify?

---

## Visualizing Clusters

```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')

for label in set(labels):
    cluster_points = positions[labels == label]
    ax.scatter(cluster_points[:,0], cluster_points[:,1], cluster_points[:,2], 
               s=1, label=f'Cluster {label}')

ax.legend()
plt.show()
```

* **Challenge**: Adjust `eps` and `min_samples` to get fewer/more clusters. What happens?

---

## Finding the Track Head (Analytical)

The track head is usually closest to the interaction origin.

**Problem**: Assume the head is the point closest to `(0,0,0)`.

```python
distances = np.linalg.norm(positions, axis=1)
head_index = np.argmin(distances)
track_head = positions[head_index]

print(f"Track head position: {track_head}")
```

* **Question**: Is this a reliable assumption? Why or why not?

---

# Summary

You've learned:

* How clustering groups electron track points
* Basic track head identification methods

Next, you'll simulate drift and understand TPC readout better!

````

---

# === Notebook 3: Simulating Drift and TPC Readout ===

```markdown
# Electron Tracks: Drift Simulation & TPC Readout

**Goal**:  
Understand electron drift in TPCs and its effect on track data.

---

## Why Drift?

In a Time Projection Chamber (TPC), electrons drift under an electric field toward a readout plane. This drift affects the positions we observe.

---

## Simulating Drift

Assume a simple drift along the Z-axis:

```python
positions_drifted = positions.copy()
drift_length = 0.05  # 5 cm drift
positions_drifted[:,2] += drift_length

fig = plt.figure(figsize=(8,6))
ax = fig.add_subplot(111, projection='3d')
ax.scatter(positions[:,0], positions[:,1], positions[:,2], s=1, alpha=0.3, label='Original')
ax.scatter(positions_drifted[:,0], positions_drifted[:,1], positions_drifted[:,2], 
           s=1, alpha=0.5, label='Drifted')

ax.legend()
ax.set_title('Track Before and After Drift')
plt.show()
````

* **Question**: How does drift affect your track visualization?

---

## TPC Readout (Pixelated)

Simulate a pixelated sensor (like GAMPix):

```python
pixel_size = 0.005  # 5mm pixels

# Simulate pixel readout (simple discretization)
pixel_x = np.round(positions_drifted[:,0] / pixel_size) * pixel_size
pixel_y = np.round(positions_drifted[:,1] / pixel_size) * pixel_size
pixel_z = np.round(positions_drifted[:,2] / pixel_size) * pixel_size

fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.scatter(pixel_x, pixel_y, pixel_z, s=1)
ax.set_title("Pixelated Readout")
plt.show()
```

* **Challenge**: Try different pixel sizes and observe changes in resolution.

---

## Interactive Problem

* **Problem**: Calculate the number of unique pixels triggered. How does this compare to original points?

```python
unique_pixels = np.unique(np.column_stack((pixel_x, pixel_y, pixel_z)), axis=0)
print(f"Unique pixels triggered: {len(unique_pixels)}")
```

* **Question**: What happens to your resolution as the pixel size grows?

---

## Final Thoughts & Challenges

* Electron drift smears out details.
* Pixel size affects resolution.

**Open Problem**:

* How would you reconstruct the original electron path from the pixel data alone?

Discuss and brainstorm ideas!

---

# Summary

You've learned:

* Drift effects and TPC readout basics
* Pixelation resolution impacts on electron tracks

You're now ready to build ML models to predict track direction and origin!

```

---
```
