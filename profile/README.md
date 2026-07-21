<div align="center">
  <img src="https://raw.githubusercontent.com/apairo-robotics/apairo/main/docs/resources/apairo_logo_full.png" alt="apairo" width="380">

  **One numpy-in / numpy-out API for robotics sensor datasets.**

  [Documentation](https://apairo-robotics.github.io/apairo/) ·
  [Core repo](https://github.com/apairo-robotics/apairo) ·
  [Bring your own dataset](https://apairo-robotics.github.io/apairo/datasets/bring-your-own-dataset/)
</div>

---

## What is apairo?

**apairo** is a unified loader for robotics sensor datasets — lidar, cameras,
poses, IMU, labels — with one chainable API across **synchronous** (KITTI-style)
and **asynchronous** (multi-rate) layouts. No bespoke `glob + np.load` loaders,
no hand-written timestamp matching: everything is a dataset you `filter`,
`select`, `cache`, `synchronize`, `concat` and feed straight to a PyTorch
`DataLoader`.

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

| Repo | What it does |
|---|---|
| **[apairo](https://github.com/apairo-robotics/apairo)** | The core — load / synchronize / filter / cache / preprocess robotics datasets, one API for sync + async layouts. |
| **[apairo_transform](https://github.com/apairo-robotics/apairo_transform)** | Access-time numpy transforms & augmentations (range/box filters, voxelization, rotations, interpolators). |
| **[apairo_preprocess](https://github.com/apairo-robotics/apairo_preprocess)** | Heavy offline preprocessors, persisted as derived `.apairo` channels and reloaded transparently. |
| **[apairo_extractor](https://github.com/apairo-robotics/apairo_extractor)** | Turn ROS bags into the apairo / KITTI on-disk layout, with optional preprocessing. |
| **[apairo_rr](https://github.com/apairo-robotics/apairo_rr)** | Rerun-based lidar / multi-sensor visualization of apairo datasets. |
| **[apairo_huggingface](https://github.com/apairo-robotics/apairo_huggingface)** | Label apairo datasets and export them to the HuggingFace `LeRobotDataset` format. |
| **[apairo_mnt](https://github.com/apairo-robotics/apairo_mnt)** | The AMIAD MNT dataset and its on-disk layout (zarr missions). |

---

## Getting started

```bash
pip install apairo          # Python >= 3.11
pip install apairo[vision]  # optional: image loading (Pillow)
```

- 📖 **[Documentation](https://apairo-robotics.github.io/apairo/)** — quickstart, the `.apairo` schema, synchronizing async sensors, preprocessing.
- 🧩 **[Bring your own dataset](https://apairo-robotics.github.io/apairo/datasets/bring-your-own-dataset/)** — YAML profiles, thin subclasses, filename-encoded keys.
- 🛠 **[apairo-robotics/apairo](https://github.com/apairo-robotics/apairo)** — source, issues, contributing.
