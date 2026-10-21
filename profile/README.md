<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/apairo-robotics/apairo/main/docs/resources/logo/apairo_logo_full_on_dark.png">
    <img src="https://raw.githubusercontent.com/apairo-robotics/apairo/main/docs/resources/apairo_logo_full.png" alt="apairo" width="380">
  </picture>

  **One numpy-in / numpy-out API for robotics sensor datasets.**

  [![Website](https://img.shields.io/badge/website-apairo--robotics.github.io-1f6feb?logo=github&logoColor=white)](https://apairo-robotics.github.io/)
  [![Docs](https://img.shields.io/badge/docs-apairo-2ea44f)](https://apairo-robotics.github.io/apairo/)
  [![PyPI](https://img.shields.io/pypi/v/apairo?logo=pypi&logoColor=white&color=3775A9)](https://pypi.org/project/apairo/)
  ![Python](https://img.shields.io/badge/python-%E2%89%A5%203.11-3776AB?logo=python&logoColor=white)
  ![NumPy](https://img.shields.io/badge/built%20on-NumPy-013243?logo=numpy&logoColor=white)
  ![License](https://img.shields.io/badge/license-MIT-2ea44f)
</div>

---

## What is apairo?

**apairo** is a unified loader for robotics sensor datasets — lidar, cameras,
poses, IMU, labels — with one chainable API across **synchronous** (every
sensor at every frame) and **asynchronous** (multi-rate) layouts. No bespoke
`glob + np.load` loaders, no hand-written timestamp matching: everything is a
dataset you `filter`, `select`, `cache`, `synchronize`, `concat` and feed
straight to a PyTorch `DataLoader`.

- **numpy in, numpy out** — `ds[i].data["lidar"] -> np.ndarray`; convert to
  torch/tf at the edge, never inside the dataset.
- **everything is a dataset** — `filter / select / cache / join / concat /
  synchronize / split / transform` all return chainable, lazy views.
- **`.apairo` is the source of truth on disk** — channels (raw + derived) are
  declared in a sidecar; expensive preprocess output is persisted and reloaded
  transparently.

```python
import apairo

ds = (apairo.RawDataset("/data/mission", keys=["lidar", "image", "gps"])
        .synchronize(reference="lidar", method="nearest", tolerance=0.05)
        .filter(lambda s: s.data["lidar"].shape[0] > 1000))

ds[0].data          # {"lidar": (N, 4), "image": (H, W, 3), "gps": (3,)}
# an apairo dataset *is* a torch Dataset -> DataLoader(ds, collate_fn=...), no adapter
```

Point `RawDataset` at a directory and it loads — no code. When a channel's clock
lives in its **filenames**, declare it right there and apairo reads it in memory,
never writing into your data. See
[Bring your own dataset](https://apairo-robotics.github.io/apairo/datasets/bring-your-own-dataset/).

---

## The ecosystem

> Mechanisms live in the **core**; collections live in **satellites**. The core
> never gains a dependency beyond numpy + PyYAML.

| Repo | Role | What it does |
|---|---|---|
| **[apairo](https://github.com/apairo-robotics/apairo)** | Core | Profile, load, synchronize, filter, cache and preprocess robotics datasets — one API for sync + async layouts. Adds the `apairo` command. |
| **[apairo_extractor](https://github.com/apairo-robotics/apairo_extractor)** | Ingest | Turn ROS bags (ROS 1, ROS 2, MCAP) into an apairo dataset, from an interactive TUI or headless. Adds `apairo extractor`. |
| **[apairo_transform](https://github.com/apairo-robotics/apairo_transform)** | Runtime | Transforms and augmentations applied lazily at access time — range/box filters, voxelization, rotations, interpolators for `synchronize()`. |
| **[apairo_preprocess](https://github.com/apairo-robotics/apairo_preprocess)** | Offline | Pipelines that compute a derived channel once — odometry, ground segmentation, traversability, projections — persisted in `.apairo` and reloaded transparently. |
| **[apairo_visu](https://github.com/apairo-robotics/apairo_visu)** | Visualise | Look at any dataset in a browser Studio, an Open3D viewer or Rerun. Adds `apairo studio`, `apairo visu` and `apairo rerun`. |

Around them: **[apairo_benchmarks](https://github.com/apairo-robotics/apairo_benchmarks)**
measures apairo against a hand-rolled loader, and
**[apairo_huggingface](https://github.com/apairo-robotics/apairo_huggingface)**
is the worked example of the whole stack, from TartanDrive rosbags to a
`LeRobotDataset` on the Hugging Face Hub.

---

## Getting started

```bash
pip install apairo            # Python >= 3.11
pip install "apairo[tartan]"  # a standard dataset: also rellis, goose, semantic-kitti, tum, euroc, kuka
apairo status /data/mission   # profile a folder before writing any code
```

- **[Documentation](https://apairo-robotics.github.io/apairo/)** — quickstart, the `.apairo` schema, synchronizing async sensors, preprocessing.
- **[Bring your own dataset](https://apairo-robotics.github.io/apairo/datasets/bring-your-own-dataset/)** — YAML profiles, thin subclasses, filename-encoded keys.
- **[apairo-robotics/apairo](https://github.com/apairo-robotics/apairo)** — source, issues, contributing.
