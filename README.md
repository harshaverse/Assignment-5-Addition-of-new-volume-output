<div align="center">

# 🔊 SU2 Custom Volume Output
## ⚡ Local Speed of Sound

![SU2](https://img.shields.io/badge/SU2-v8.4.0%20Harrier-blue?style=for-the-badge&logo=airplayvideo&logoColor=white)
![CFD](https://img.shields.io/badge/CFD-Compressible%20Flow-orange?style=for-the-badge)
![Language](https://img.shields.io/badge/Language-C%2B%2B%20%7C%20Python-green?style=for-the-badge&logo=cplusplus)
![Viz](https://img.shields.io/badge/Visualization-ParaView-purple?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete%20✅-brightgreen?style=for-the-badge)

<br>

> 🚀 **Extending SU2's output system to visualize local acoustic speed around a NACA0012 airfoil**

<br>

👤 **Boddu Harshavardhan** &nbsp;|&nbsp; 🎓 B.Tech Mechanical Engineering &nbsp;|&nbsp; 🏫 GVP College of Engineering

</div>

---

## 🌟 What's This Project About?

SU2 is a powerful open-source CFD solver — but it doesn't export **every** thermodynamic variable to ParaView by default.

This project **hacks into SU2's output pipeline** to add a brand-new variable:

```
🔊  SPEED_OF_SOUND  →  exported to .vtu  →  visualized in ParaView
```

We then run it on the classic **NACA0012 inviscid airfoil** case and watch the acoustic field come to life! 🛩️

### ✅ What We Did

| # | Task | Status |
|---|------|--------|
| 1 | 📝 Registered `SPEED_OF_SOUND` in SU2's output system | ✅ Done |
| 2 | ⚙️ Computed $a = \sqrt{\gamma p / \rho}$ at every mesh node | ✅ Done |
| 3 | 💾 Exported variable to `.vtu` ParaView file | ✅ Done |
| 4 | 🎨 Visualized contour distribution around airfoil | ✅ Done |

---

## 📐 The Physics — Speed of Sound

In compressible flow, the **local speed of sound** is defined as:

$$\boxed{a = \sqrt{\gamma \frac{p}{\rho}}}$$

| Symbol | Meaning | Unit |
|--------|---------|------|
| $a$ | 🔊 Local speed of sound | m/s |
| $\gamma$ | 🌡️ Ratio of specific heats (1.4 for air) | — |
| $p$ | 💨 Static pressure | Pa |
| $\rho$ | ⚖️ Local density | kg/m³ |

> 💡 **Why does it vary?** As flow accelerates over the airfoil, pressure and density change — and so does the local speed of sound. Compression zones are *faster*, expansion zones are *slower*.

---

## 🛠️ Implementation — Under the Hood

> **File modified:** `SU2_CFD/output/CFlowOutput.cpp`

### 🔵 Step 1 — Register the Variable

Inside `SetVolumeOutputFieldsScalarPrimitive()`, add:

```cpp
// 🆕 Register custom speed of sound output
AddVolumeOutput("SPEED_OF_SOUND", "Speed_of_Sound", "PRIMITIVE", "Local speed of sound");
```

This tells SU2: *"hey, I want this variable to appear in the output file!"* 📢

---

### 🟠 Step 2 — Compute It at Every Node

Inside `LoadVolumeDataScalar()`, add:

```cpp
// 🔊 Compute local speed of sound: a = sqrt(gamma * p / rho)
su2double gamma = config->GetGamma();
su2double rho   = Node_Flow->GetDensity(iPoint);
su2double p     = Node_Flow->GetPressure(iPoint);

su2double a = sqrt(gamma * p / rho);   // 🎯 The magic line

SetVolumeOutputValue("SPEED_OF_SOUND", iPoint, a);
```

Two variables in → one acoustic speed out. Simple, elegant, powerful. ✨

---

## 🔨 Build & Install

After saving your changes, recompile SU2:

```bash
# 🏗️ Navigate to SU2 root
cd ~/SU2

# ⚡ Build with ninja (fast!)
ninja -C build

# 📦 Install
sudo ninja -C build install
```

---

## ▶️ Run the Simulation

### 🖥️ Serial
```bash
cd ~/SU2/TestCases/euler/naca0012
SU2_CFD inv_NACA0012.cfg
```

### 🚀 Parallel (MPI — 4 cores)
```bash
mpirun --oversubscribe -np 4 SU2_CFD inv_NACA0012.cfg
```

> 💪 More cores = faster solution. Scale up as needed!

---

## 📂 Output Files

```
📁 naca0012/
 ├── 📊 history.csv          ← convergence residuals per iteration
 ├── 🌊 flow.vtu             ← full volume field (open in ParaView!)
 ├── 🛩️  surface_flow.vtu    ← surface quantities on airfoil
 ├── 🔄 restart_flow.dat     ← restart checkpoint
 └── 📋 forces_breakdown.dat ← lift & drag summary
```

---

## 🎨 Visualization in ParaView

```
1. 📂  Open  →  flow.vtu
2. 🔍  Select variable  →  Speed_of_Sound
3. 🌈  Apply coloring  →  Cool-to-Warm colormap
```

| Region | Speed of Sound | Why? |
|--------|---------------|------|
| 🔴 Leading edge stagnation | **Higher** (~1.3) | Compression increases $p/\rho$ |
| 🔵 Suction surface peak | **Lower** (~1.0) | Expansion decreases $p/\rho$ |
| 🟢 Far field | **Uniform** | Undisturbed free stream |

> 📊 **Observed non-dimensional range:** `1.0 – 1.3`

The smooth, physically consistent contours confirm the implementation is correct! ✅

---

## 🗂️ Repository Structure

```
📦 SU2/
 ├── 🔧 SU2_CFD/
 │   └── 📁 output/
 │       └── ✏️  CFlowOutput.cpp     ← THE modified file
 │
 ├── 🧪 TestCases/
 │   └── 📁 euler/
 │       └── 📁 naca0012/
 │           ├── ⚙️  inv_NACA0012.cfg
 │           ├── ⚙️  inv_NACA0012_Roe.cfg
 │           └── 🕸️  mesh_NACA0012_inv.su2
 │
 └── 📖 README.md
```

---

> Customizing SU2's output system unlocks a world of possibilities for CFD researchers.

🔬 **Research** — Extract any derived thermodynamic quantity for analysis  
🛠️ **Development** — Prototype new physics models and verify them visually  
📊 **Post-processing** — Avoid manual computation in ParaView with Python  
🎓 **Education** — Understand how professional CFD solvers are structured  

---

## 📚 References

- 🌐 [SU2 Official Website](https://su2code.github.io/)
- 📖 [SU2 Output System Docs](https://su2code.github.io/docs/Custom-Output/)
- 🐙 [SU2 GitHub Repository](https://github.com/su2code/SU2)
- 📄 SU2 version: **8.4.0 "Harrier"**

---

<div align="center">

Made with 🔥 and lots of ☕ &nbsp;|&nbsp; GVP College of Engineering &nbsp;|&nbsp; 2026

⭐ *Star this repo if it helped you extend SU2!* ⭐

</div>
