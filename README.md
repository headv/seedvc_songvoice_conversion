# 🎤 Seed-VC Song Voice Conversion — Kaggle Notebook

**Zero-shot singing voice timbre conversion using [Seed-VC](https://github.com/Plachtaa/seed-vc), optimized for Kaggle free GPU.**

> 零样本歌声音色转换：无需训练，用 20~30 秒参考人声，替换完整歌曲的音色。

---

## What it does

| Input | Description |
|-------|-------------|
| `reference.wav` | 20–30s clean vocal of the **target timbre** (who you want to sound like) |
| `source.wav` | Full-length song vocal to be converted |

**Output:** Same melody and timing as `source.wav`, with the voice characteristics of `reference.wav`.

---

## Quick Start

### 1. Prerequisites — Prepare clean vocal files

Both audio files **must be clean vocals with no background music**.  
Use one of these tools to separate vocals first:

- [UVR5 (Ultimate Vocal Remover)](https://github.com/Anjok07/ultimatevocalremovergui) — desktop app, recommended
- [Demucs](https://github.com/facebookresearch/demucs) — command line

### 2. Upload audio to Kaggle Dataset

1. Go to [kaggle.com/datasets](https://www.kaggle.com/datasets) → **New Dataset**
2. Upload your `reference.wav` and `source.wav`
3. Note the dataset path (e.g. `/kaggle/input/your-dataset-name/`)

### 3. Open the notebook on Kaggle

[![Open in Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://www.kaggle.com/)

Or manually:
1. Go to [kaggle.com/code](https://www.kaggle.com/code) → **New Notebook**
2. Upload `seed-vc-kaggle.ipynb`
3. Right panel → **Accelerator** → select `GPU T4 x2`
4. Left panel → **Input** → Add your dataset

### 4. Configure Cell 1

Edit `REF_PATH` and `SRC_PATH` to match your Kaggle Dataset paths:

```python
REF_PATH = "/kaggle/input/your-dataset-name/reference.wav"
SRC_PATH = "/kaggle/input/your-dataset-name/source.wav"
```

### 5. Run cells in order

```
Cell 1  →  Set paths & parameters
Cell 2  →  Verify GPU
Cell 3  →  Validate input files
Cell 4  →  Install Seed-VC (~3–5 min)
Cell 4.5→  Fix protobuf conflict (Kaggle only)
Cell 5  →  Copy audio to working directory
Cell 6  →  Test on 20s preview (~1–3 min) ← always run this first
Cell 7  →  Full song inference (~15–30 min)
Cell 8  →  Package & download results
```

---

## Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `DIFFUSION_STEPS` | 40 | Quality vs speed. 40–60 recommended for singing. |
| `LENGTH_ADJUST` | 1.0 | Duration multiplier. 1.07 gives smoother phrase endings. |
| `CFG_RATE` | 0.7 | Guidance strength. Higher = more like reference; >0.8 may add noise. |
| `AUTO_F0` | False | Auto pitch correction. Keep False to preserve original key. |
| `SEMITONE_SHIFT` | 0 | Pitch shift. 0 = no transposition. |

### Tuning guide

| Problem | Solution | Parameter |
|---------|----------|-----------|
| Output doesn't sound like reference | Increase guidance | `CFG_RATE = 0.8` |
| Too noisy or unnatural | Decrease guidance | `CFG_RATE = 0.5` |
| Pitch too high/low | Adjust semitones | `SEMITONE_SHIFT = ±1~3` |
| Output duration changed | Lock duration | `LENGTH_ADJUST = 1.0` |
| Output sounds blurry | More diffusion steps | `DIFFUSION_STEPS = 60` |
| Inference too slow | Fewer diffusion steps | `DIFFUSION_STEPS = 25` |

---

## Troubleshooting

**❌ No GPU detected**  
→ Right panel → Settings → Accelerator → select `GPU T4 x2`

**❌ File not found**  
→ Check that your Dataset is mounted (left Input panel).  
→ Verify `REF_PATH`/`SRC_PATH` paths are exact (case-sensitive, watch for spaces).

**Silent or distorted output**  
→ Run diagnostic cells 6.1 ~ 6.3 to check audio amplitude and channel content.

**pip dependency conflict warnings**  
→ Safe to ignore. Cell 4.5 resolves the critical protobuf conflict.

---

## Notes

- Seed-VC converts **timbre**, not singing style. The output keeps the melody of `source.wav`.
- Model weights (~1–2 GB) are downloaded automatically on first inference.
- Kaggle free GPU has **weekly usage limits** — always test with Cell 6 before running Cell 7.

---

## Credits

- [Seed-VC](https://github.com/Plachtaa/seed-vc) by Plachtaa — the underlying voice conversion model
- This notebook adds Kaggle-optimized setup, bilingual documentation, smart preview extraction, and post-processing pipeline.

---

## License

This notebook is released under the MIT License.  
Seed-VC is subject to its own license — see the [official repository](https://github.com/Plachtaa/seed-vc).
