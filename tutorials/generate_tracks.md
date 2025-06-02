# Generation of PENELOPE Tracks Using the S3DF Server

This tutorial describes how to generate electron or photon tracks using the **PENELOPE** simulation tool via the SLAC S3DF computing cluster. 
## 📂 Location
All relevant files are located in:
```
/sdf/group/kipac/users/gammaTPC/GenTracks
```

## 🧠 Overview

The script `generate_pen_tracks_on_cluster.py` does the following:

1. Defines track generation settings (energy, number of tracks, particle type, material).
2. Automatically creates batch Python scripts and SLURM `.sbatch` files.
3. Submits jobs to the S3DF SLURM scheduler.
4. Saves outputs in a versioned `Tracks` directory.

---

## ⚙️ Prerequisites

Before running the script:

* Ensure that `Gampy` is installed and accessible.

* Confirm that the PENELOPE executable exists at:

  ```
  ./execute
  ```

* The environment must support SLURM (`sbatch` available).

---

## 📄 Script Usage

### 1. Edit User Settings in `genTracks.py`

At the top of the script, modify these settings to suit your needs:

```python
PARTICLES = 'electrons'  # or 'photons'
MATERIAL  = 'LAr'
MAX_BATCH = 250  # max number of tracks per job

TRACK_CONFIGS = [
    {'energy': 100, 'num_tracks': 100},
    {'energy': 300, 'num_tracks': 100},
    # Add more energy configurations here
    # energy is in keV
]
```

### 2. Run the Script

From the main directory:

```bash
python3 generate_pen_tracks_on_cluster.py
```

This will:

* Create a directory `sbatch_scripts/` with all generated scripts.
* Create a new `Tracks/` output folder with a timestamp if one already exists.
* Submit jobs via SLURM automatically.

---

## 📁 Output

Each batch job will generate Penelope tracks and save results in:

```
Tracks/{material}/{particle_type}/E{energy}/
```

You can expect one folder per energy level.

---

## 🔄 Re-running the Script

If `sbatch_scripts/` or `Tracks/` already exists, they will either be:

* **Deleted** (for `sbatch_scripts`)
* **Renamed** with a timestamp (for `Tracks`)

This ensures no overwriting of old results.

---

## 🧪 Example Output Message

```bash
✅ Submitted batch 1 for 100 keV
✅ Submitted batch 2 for 100 keV
✅ Submitted batch 1 for 300 keV
...
📦 All # jobs launched.
```

---

## 🔍 Troubleshooting

* ❌ **Executable not found**:
  Ensure `./execute` is present and executable.

* ❌ **Gampy not installed**:
  Install `Gampy` into your Python environment.

* 🔒 **Permission errors**:
  Make sure the output directory is writable.

---

## 📌 Notes

* Each job is submitted using SLURM and has a memory limit of 4G and 2 hours runtime.
* Output logs are currently directed to `/dev/null`. You can modify the `SBATCH_TEMPLATE` to enable logging if desired.

