<div align="center">

# 🧙‍♂️ The Invisible Cloak
### *"Of course it is happening inside your head, but why on earth should that mean that it is not real?"*
— Albus Dumbledore

---

<!-- Harry Potter Invisibility Cloak GIF -->
<img src="https://media.giphy.com/media/Ju7l5y9osyymQ/giphy.gif" width="280" alt="Harry Potter Invisibility Cloak"/>

&nbsp;

<!-- YOUR demo GIF — replace the src below with your own GIF URL -->
<img src="https://YOUR_DEMO_GIF_LINK_HERE.gif" width="280" alt="Invisible Cloak Demo"/>

&nbsp;

<!-- YOUR binary mask GIF — replace the src below with your binary mask GIF/video URL -->
<img src="https://YOUR_BINARY_MASK_GIF_LINK_HERE.gif" width="280" alt="Binary Mask — Red Region Detected"/>

> *🧙 The original magic &nbsp;|&nbsp; 🎬 Cloak effect in action &nbsp;|&nbsp; 🔴 Binary mask — red region detected*

---

[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.x-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)](https://opencv.org)
[![NumPy](https://img.shields.io/badge/NumPy-1.x-013243?style=for-the-badge&logo=numpy&logoColor=white)](https://numpy.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

</div>

---

## 📜 The Scroll of Secrets — What is This?

Ever watched Harry Potter pull on his Invisibility Cloak and vanish before your very eyes? This project recreates that same sorcery — not with ancient magic, but with **computer vision**.

By wrapping themselves in a **red blanket**, a person disappears entirely from the video — replaced seamlessly by the background behind them. No green screen. No CGI. Just pure pixel-level wizardry using **OpenCV** and **NumPy**.

▶️ **[Watch the Full Invisible Cloak Demo Video](https://YOUR_VIDEO_LINK_HERE)** ← *(replace with your link)*

The project also outputs a **binary mask video** — a debug view that isolates only the red region detected in each frame. Wherever the blanket is, the background peeks through against pure black. It's a great way to understand exactly what the algorithm is "seeing" before the final blend.

<div align="center">
<img src="https://YOUR_BINARY_MASK_GIF_LINK_HERE.gif" width="480" alt="Binary Mask Video"/>

*🔴 Binary mask — the red blanket region isolated frame by frame*
</div>

---

## ✨ The Magic Explained — How It Works

The spell is cast in four incantations:

```
📽️  Frame Read  →  🔴 Red Detection  →  🎭 Masking  →  🖼️ Background Blend
```

### 1. 📽️ Capturing the Background
The **very first frame** of the video is treated as the clean background — the world as it looks *before* the cloak appears. This reference image is what gets "revealed" wherever the cloak is detected.

### 2. 🔴 Detecting the Cloak (HSV Color Masking)
Each frame is converted from **BGR → HSV** color space. HSV is used instead of raw RGB because it separates *color (Hue)* from *brightness (Value)*, making red detection far more robust under varying lighting.

Red is a tricky color in HSV — it wraps around the 0°/360° boundary. So we define **two detection ranges** and combine them:

| Range | Lower Bound `[H, S, V]` | Upper Bound `[H, S, V]` |
|-------|------------------------|------------------------|
| Lower Red | `[0, 140, 40]` | `[10, 255, 255]` |
| Upper Red | `[170, 140, 40]` | `[180, 255, 255]` |

### 3. 🧹 Morphological Cleanup
The raw mask is noisy. Two morphological operations clean it up:
- **MORPH_OPEN** — Erodes then dilates. Kills tiny noise speckles.
- **MORPH_DILATE** — Expands the mask outward to fill edge gaps around the cloak.

### 4. 🎭 The Vanishing Act (Bitwise Masking)

| Operation | Effect |
|-----------|--------|
| `bitwise_and(frame, frame, mask=non_red_mask)` | Keep everything *outside* the cloak |
| `bitwise_and(background, background, mask=red_mask)` | Show the background *through* the cloak |
| `addWeighted(foreground, 1, cloak_region, 1, 0)` | Combine both into the final frame |

Since the masks are perfectly complementary (no overlap), `addWeighted` simply stitches the two regions together.

---

## 🗂️ Project Structure

```
invisible-cloak/
│
├── invisible_cloak.py        # 🧙 Main spell — the full pipeline
├── README.md                 # 📜 You are here
│
├── input/
│   └── Person_Covered_With_Red_Blanket.mp4   # Input video
│
└── output/
    ├── invisibleCloak.mp4          # 🎬 Final cloak effect video
    └── BinaryInvisibleCloak.mp4   # 🎭 Debug: binary mask video
```

---

## ⚗️ Requirements

```bash
pip install opencv-python numpy
```

| Library | Purpose |
|---------|---------|
| `opencv-python` | Frame capture, HSV conversion, morphological ops, video writing |
| `numpy` | Array operations for mask construction |

---

## 🔮 Setting Up the Spell

### 1. Clone the Repository
```bash
git clone https://github.com/YOUR_USERNAME/invisible-cloak.git
cd invisible-cloak
```

### 2. Install Dependencies
```bash
pip install opencv-python numpy
```

### 3. Configure Your Paths
Open `invisible_cloak.py` and update the three path variables at the top:

```python
INPUT_VIDEO      = "path/to/your/input_video.mp4"
OUTPUT_VIDEO     = "path/to/output/invisibleCloak.mp4"
OUTPUT_BINARY_VIDEO = "path/to/output/BinaryInvisibleCloak.mp4"
```

### 4. Cast the Spell 🪄
```bash
python invisible_cloak.py
```

A preview window will show the background frame — press **any key** to begin processing.

---

## 🎥 Recording Tips for Best Results

> *"Even the most powerful magic needs the right conditions."*

| Tip | Why it matters |
|-----|---------------|
| 🔴 Use a **solid, bright red** object | Easier to detect with high saturation |
| 💡 Record in **good, consistent lighting** | Prevents the mask from missing shadowed areas |
| 📷 Keep the **camera perfectly still** | A moving camera ruins the background reference |
| 🎬 Start with **1–2 seconds of clear background** | Gives a clean reference frame before the cloak appears |
| 🚫 Avoid red clothing or objects in the scene | They will be treated as part of the cloak! |

---

## 🔧 Tweaking the Magic

**Too much noise / flickering mask?**
→ Increase the `MORPH_KERNEL` size (e.g. `(5, 5)`) or add extra MORPH_OPEN iterations.

**Cloak edges look rough?**
→ Try applying `cv2.GaussianBlur` to the mask before the bitwise operations.

**Wrong colors being detected?**
→ Tune `LOWER_RED_1`, `UPPER_RED_1`, `LOWER_RED_2`, `UPPER_RED_2` using a HSV color picker.

**Want real-time webcam mode?**
→ Replace `cv2.VideoCapture(INPUT_VIDEO)` with `cv2.VideoCapture(0)` and display frames using `cv2.imshow`.

---

## 🌟 The Invisibility Pipeline — Visual Summary

```
┌─────────────────────────────────────────────────────────────┐
│                     INPUT VIDEO FRAME                        │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │  Convert BGR → HSV  │
              └──────────┬──────────┘
                         │
                         ▼
          ┌──────────────────────────────┐
          │   Detect Red (2 HSV ranges)  │
          │   mask = mask1 + mask2       │
          └──────────────┬───────────────┘
                         │
                         ▼
          ┌──────────────────────────────┐
          │  Morphological Cleanup       │
          │  OPEN → remove noise         │
          │  DILATE → fill edges         │
          └──────────────┬───────────────┘
                         │
             ┌───────────┴────────────┐
             ▼                        ▼
   ┌──────────────────┐    ┌────────────────────┐
   │  non_red_mask    │    │   red_mask          │
   │  (inverted)      │    │   (cloak region)    │
   └────────┬─────────┘    └─────────┬──────────┘
            │                        │
            ▼                        ▼
   ┌──────────────────┐    ┌────────────────────┐
   │ foreground       │    │  background pixels  │
   │ (current frame)  │    │  (first frame)      │
   └────────┬─────────┘    └─────────┬──────────┘
            │                        │
            └───────────┬────────────┘
                        ▼
              ┌──────────────────┐
              │   addWeighted    │
              │   (blend both)   │
              └────────┬─────────┘
                       │
                       ▼
            ┌──────────────────────┐
            │   FINAL OUTPUT FRAME │
            └──────────────────────┘
```

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

*"It does not do to dwell on dreams and forget to live."*
— but feel free to dwell on this project a little. 🧙‍♂️✨

**Made with 🪄 and OpenCV**

</div>
