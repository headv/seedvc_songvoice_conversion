# 🎤 Seed-VC 歌声音色转换 | Song Voice Conversion — Kaggle Notebook

**零样本歌声音色转换：无需训练，用 20~30 秒参考人声，替换完整歌曲的音色。**  
**Zero-shot singing voice timbre conversion — no training required, runs on Kaggle free GPU.**

基于 [Seed-VC](https://github.com/Plachtaa/seed-vc) 官方模型，针对 Kaggle 免费 GPU 环境优化。  
Powered by the official [Seed-VC](https://github.com/Plachtaa/seed-vc) model, optimized for Kaggle free GPU.

---

## 功能说明 | What it does

| 输入 Input | 说明 Description |
|-----------|-----------------|
| `reference.wav` | 不低于20秒目标音色参考人声（你想转换成谁的声音）<br>More than 20s clean vocal of the **target timbre** |
| `source.wav` | 需要被转换的完整歌曲人声<br>Full-length song vocal to be converted |
| 参考人声需用外部软件（Au/剪影/UVR5等）提取出纯人声不含配乐| 
| You must use external software (such as Au, Capcut, UVR5, etc.) to extract the pure vocal track, free of any background music.| 

**输出 Output：** 保留 `source.wav` 的旋律和节奏，音色替换为 `reference.wav` 的声音特征。  
Same melody and timing as `source.wav`, with the voice characteristics of `reference.wav`.

---

## 快速开始 | Quick Start

### 第一步 Step 1 — 准备纯人声文件 | Prepare clean vocal files

两个音频**必须是纯人声，无背景音乐**，否则转换效果会很差。  
Both files **must be clean vocals with no background music**, or conversion quality will be poor.

推荐使用以下工具提前拆轨 | Recommended tools for vocal separation:

- [UVR5 (Ultimate Vocal Remover)](https://github.com/Anjok07/ultimatevocalremovergui) — 桌面应用，推荐 | Desktop app, recommended
- [Demucs](https://github.com/facebookresearch/demucs) — 命令行工具 | Command line

### 第二步 Step 2 — 上传音频到 Kaggle Dataset | Upload audio to Kaggle Dataset

1. 打开 [kaggle.com/datasets](https://www.kaggle.com/datasets) → **New Dataset**  
   Go to [kaggle.com/datasets](https://www.kaggle.com/datasets) → **New Dataset**
2. 上传你的 `reference.wav` 和 `source.wav`  
   Upload your `reference.wav` and `source.wav`
3. 记下 Dataset 路径，例如 `/kaggle/input/your-dataset-name/`  
   Note the dataset path, e.g. `/kaggle/input/your-dataset-name/`

### 第三步 Step 3 — 在 Kaggle 中打开 Notebook | Open the notebook on Kaggle

[![Open in Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://www.kaggle.com/)

手动导入步骤 | Or manually:
1. 打开 [kaggle.com/code](https://www.kaggle.com/code) → **New Notebook** → 上传 `seed-vc-songvoice_conversion_kaggle.ipynb`  
   Go to [kaggle.com/code](https://www.kaggle.com/code) → **New Notebook** → upload the `.ipynb` file
2. 上方菜单选择 **Setting** → **Accelerator** → 选择 `GPU T4 x2`  
   Top Menu Choose **Setting**  → **Accelerator** → select `GPU T4 x2`
3. 右侧面板 → **Input** → 添加你的 Dataset（音频路径添加后可以复制）  
   Right panel → **Input** → Add your dataset(The audio path can be copied after it is added.)
   

### 第四步 Step 4 — 修改 Cell 1 中的路径 | Configure Cell 1

将 `REF_PATH` 和 `SRC_PATH` 改为你的实际路径 | Edit to match your actual paths:

```python
REF_PATH = "/kaggle/input/your-dataset-name/reference.wav"
SRC_PATH = "/kaggle/input/your-dataset-name/source.wav"
```

### 第五步 Step 5 — 按顺序运行 Cell | Run cells in order

```
Cell 1   →  配置路径和参数       Set paths & parameters
Cell 2   →  检查 GPU 环境        Verify GPU
Cell 3   →  验证音频文件          Validate input files
Cell 4   →  安装 Seed-VC (~3–5 min)   Install Seed-VC
Cell 4.5 →  修复依赖冲突          Fix protobuf conflict (Kaggle only)
Cell 5   →  复制音频到工作目录    Copy audio to working directory
Cell 6   →  20秒片段测试推理 (~1–3 min) ← 务必先跑这个！Test first!
Cell 7   →  完整歌曲推理 (~15–30 min)   Full song inference
Cell 8   →  打包下载结果          Package & download results
```

---

## 参数说明 | Parameters

| 参数 Parameter | 默认值 Default | 说明 Description |
|---------------|--------------|-----------------|
| `DIFFUSION_STEPS` | 40 | 扩散步数，越高质量越好但越慢，推荐 40~60<br>Quality vs speed. 40–60 recommended for singing. |
| `LENGTH_ADJUST` | 1.0 | 时长调整系数，1.07 可让尾音更自然<br>Duration multiplier. 1.07 gives smoother phrase endings. |
| `CFG_RATE` | 0.7 | 引导强度，越高越贴近参考音色，>0.8 可能引入噪音<br>Guidance strength. Higher = more like reference; >0.8 may add noise. |
| `AUTO_F0` | False | 自动音高校正，保持 False 以保留原调<br>Auto pitch correction. Keep False to preserve original key. |
| `SEMITONE_SHIFT` | 0 | 半音偏移，0 = 不移调，正数升调，负数降调<br>Pitch shift in semitones. 0 = no transposition. |

### 效果调参指南 | Tuning Guide

| 问题 Problem | 解决方向 Solution | 参数 Parameter |
|-------------|-----------------|---------------|
| 音色不够像参考声音 / Not like reference | 提高引导强度 Increase guidance | `CFG_RATE = 0.8` |
| 噪音大或过于夸张 / Too noisy | 降低引导强度 Decrease guidance | `CFG_RATE = 0.5` |
| 音调整体偏高或偏低 / Pitch off | 调整半音 Adjust semitones | `SEMITONE_SHIFT = ±1~3` |
| 输出时长变了 / Duration changed | 锁定时长 Lock duration | `LENGTH_ADJUST = 1.0` |
| 输出模糊不清晰 / Output blurry | 增加扩散步数 More steps | `DIFFUSION_STEPS = 60` |
| 推理太慢 / Too slow | 减少扩散步数 Fewer steps | `DIFFUSION_STEPS = 25` |

---

## 常见问题 | Troubleshooting

**❌ 未检测到 GPU / No GPU detected**  
→ 右侧面板 → Settings → Accelerator → 选择 `GPU T4 x2`  
→ Right panel → Settings → Accelerator → select `GPU T4 x2`

**❌ 文件不存在 / File not found**  
→ 确认 Dataset 已在左侧 Input 面板挂载，Cell 1 中路径拼写完全一致（区分大小写）  
→ Check Dataset is mounted in left Input panel. Verify paths are exact (case-sensitive).

**输出无声或失真 / Silent or distorted output**  
→ 运行诊断 Cell 6.1 ~ 6.3 检查音频振幅和声道  
→ Run diagnostic cells 6.1 ~ 6.3 to check audio amplitude and channels.

**pip 依赖冲突警告 / pip dependency conflict warnings**  
→ 可以忽略，Cell 4.5 已处理关键的 protobuf 冲突  
→ Safe to ignore. Cell 4.5 resolves the critical protobuf conflict.

---

## 注意事项 | Notes

- Seed-VC **转换的是音色，不是唱法**。输出保留 `source.wav` 的旋律，换上 `reference.wav` 的声音特征。  
  Seed-VC converts **timbre**, not singing style. Melody comes from `source.wav`.

- 两个音频**必须是纯人声**（已用 UVR5/Demucs 拆轨），有背景音乐会严重影响效果。  
  Both files **must be clean vocals** (use UVR5/Demucs first). Background music degrades quality significantly.

- 模型权重首次推理时自动下载（约 1~2 GB，会话内缓存）。  
  Model weights (~1–2 GB) are downloaded automatically on first inference and cached.

- Kaggle 免费 GPU 有**每周时长限制**，务必先用 Cell 6 测试通过再跑完整版。  
  Kaggle free GPU has **weekly usage limits** — always test with Cell 6 before running Cell 7.

---

## 致谢 | Credits

- [Seed-VC](https://github.com/Plachtaa/seed-vc) by Plachtaa — 底层语音转换模型 | The underlying voice conversion model
- 本 Notebook 在官方基础上增加了：Kaggle 环境适配、中英双语文档、智能预览片段提取、后处理响度归一化流程。  
  This notebook adds: Kaggle-optimized setup, bilingual docs, smart preview extraction, and loudness normalization post-processing.

---

## 许可证 | License

本 Notebook 采用 MIT 许可证开源。  
This notebook is released under the MIT License.

Seed-VC 模型遵循其自身许可证，详见 [官方仓库](https://github.com/Plachtaa/seed-vc)。  
Seed-VC is subject to its own license — see the [official repository](https://github.com/Plachtaa/seed-vc).
