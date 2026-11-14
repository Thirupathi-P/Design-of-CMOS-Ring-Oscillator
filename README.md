<h1 align="center">Design of CMOS Ring Oscillator</h1>

<p align="center">
  3-Stage Ring Oscillator · GPDK 90 nm Technology · Cadence Virtuoso · Target Frequency 15 GHz
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Target-15%20GHz-blue?style=for-the-badge" alt="Target">
  <img src="https://img.shields.io/badge/Before%20layout-14.72%20GHz-brightgreen?style=for-the-badge" alt="Before layout">
  <img src="https://img.shields.io/badge/After%20layout-10.87%20GHz-red?style=for-the-badge" alt="After layout">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/DRC%20%2F%20LVS-passed-brightgreen?style=for-the-badge" alt="DRC LVS">
  <img src="https://img.shields.io/badge/Technology-GPDK%2090%20nm-orange?style=for-the-badge" alt="Technology">
</p>

> [!TIP]
> 📄 **Recommended: Read the full [project report (PDF)](./Design_of_CMOS_RO.pdf) first.** It contains the theory, calculations, diagrams, waveforms and detailed analysis. This README is a short summary of it.

**Tools:** Cadence Virtuoso · Spectre · Assura / Quantus · GPDK 90 nm PDK

---

## Contents

1. [Ring Oscillator](#ring-oscillator)
2. [Full Custom Flow](#1-full-custom-flow)
3. [Circuit Design](#2-circuit-design) – [Schematic](./Schematic/) · [Symbol](./Symbol/)
4. [Circuit Simulation](#3-circuit-simulation) – [Testbench](./Testbench/) · [Pre-layout results](./Prelayout_Simulation/)
5. [Layout Design](#4-layout-design) – [Layout](./Layout/) · [DRC](./DRC/) · [LVS](./LVS/)
6. [Post Layout Simulation](#5-post-layout-simulation) – [Extracted view](./Extracted_View/) · [Post-layout results](./Postlayout_Simulation/)
7. [PVT Analysis](#6-pvt-analysis) – [PVT plots](./PVT_Analysis/)
8. [GDS II](#7-gds-ii)
9. [Why the frequency dropped](#why-the-frequency-dropped)
10. [Suggested Improvements](#suggested-improvements)
11. [Repository Structure](#repository-structure)
---

## Ring Oscillator

A ring oscillator is a self-starting timing source built only from inverters, with no input signal. This project designs a **3-stage CMOS ring oscillator** in GPDK 90 nm with a **15 GHz** target.

> **In simple terms:** Every chip needs a clock that ticks billions of times per second. This project builds one from three NOT gates in a loop, then compares its speed before and after it is drawn as a real layout, where the wires slow it down.
>


#### Description:
- Circuit: 3-stage single-ended CMOS ring oscillator
- Technology: GPDK 90 nm
- Target frequency: 15 GHz at 27 °C

---


## 1. Full Custom Flow

Full custom means every transistor is sized and every wire is drawn by hand, then checked and re-simulated with the real layout parasitics. This project follows the flow below.

> [!IMPORTANT]
> ## Design Flow
> 
> ```text
> Schematic ➔ Symbol ➔ Testbench ➔ Pre-layout Simulation ➔ Layout ➔ DRC ➔ LVS ➔ Extraction ➔ Post-layout Simulation ➔ PVT Analysis ➔ GDSII
> ```
> 

## 2. Circuit Design

### Schematic

| Parameter | Value |
|------|---|
| Stages (N) | 3 |
| Channel length (L) | 100 nm |
| NMOS width (Wn) | 230 nm |
| PMOS width (Wp) | 615 nm |
| Supply (Vdd) | 1.2 V |

Sizes and supply were chosen from parametric sweeps. For calculations and sweep plots, **refer to the [report](./Design_of_CMOS_RO.pdf) (Chapter 2)**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/7e4734ef-5df9-48b3-b06a-9051907dff4b" width="700" alt="Schematic of the 3-stage ring oscillator"><br>
  <i>Fig. 1: Schematic of the 3-stage ring oscillator</i>
</p>

📁 More files: [Schematic/](./Schematic/)

### Symbol

The three inverters are connected in a closed loop and packaged as a symbol with pins **Vdd**, **Vss** and **Vout**. There is no input pin.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b76c30b4-5508-4933-bbfe-4f66602a231e" width="700" alt="Symbol of the ring oscillator"><br>
  <i>Fig. 2: Symbol of the ring oscillator</i>
</p>

📁 More files: [Symbol/](./Symbol/)

---

## 3. Circuit Simulation

*Inputs: supply, temperature and device models.* The testbench is in [Testbench/](./Testbench/).

### Transient Response

Transient analysis in Spectre at 27 °C. An initial condition (Vout = 0) starts the oscillation in the simulator; in a real circuit, noise does this.

<p align="center">
  <img src="https://github.com/user-attachments/assets/a3fc69c6-a963-4f91-a061-88d405365634" width="700" alt="Output of each stage versus time"><br>
  <i>Fig. 3: Output of each stage versus time</i>
</p>

- Frequency: **14.72 GHz** (1.9% below target)

📁 Setup and results: [Prelayout_Simulation/](./Prelayout_Simulation/) · Waveforms: [Waveforms/](./Waveforms/)

### Frequency and Power of Ring Oscillator

<p align="center">
  <img src="https://github.com/user-attachments/assets/09c0fa6c-5c49-4c73-ae79-fe1c0045a179" width="700" alt="Average power"><br>
  <i>Fig. 4: Frequency Measurement and Power consumption</i>
</p>

- Average power: **182.5 µW**
- Energy per cycle: **12.4 fJ**

📁 More files: [Prelayout_Simulation/](./Prelayout_Simulation/)

---

## 4. Layout Design

- Three identical inverters are placed side by side in the same orientation.
- The third output is routed back to the first. This feedback wire is the longest net.

### Layout

<p align="center">
  <img src="https://github.com/user-attachments/assets/60726112-1eeb-4787-849f-6dde4f6d4511" width="700" alt="Layout of the ring oscillator"><br>
  <i>Fig. 5: Layout of the 3-stage ring oscillator</i>
</p>

📁 More files: [Layout/](./Layout/)

**DRC and LVS**

- **DRC (Design Rule Check):** no errors. See [DRC/](./DRC/).
- **LVS (Layout versus Schematic):** schematic and layout match. See [LVS/](./LVS/).

---

## 5. Post Layout Simulation

Wire resistance and capacitance were extracted with **Quantus** into the `av_extracted` view ([Extracted_View/](./Extracted_View/)), and the simulation was repeated on it.

### Transient Response (Post-layout)

<p align="center">
  <img src="https://github.com/user-attachments/assets/3a3265cd-bfc2-44b5-b22c-18a978770217" width="700" alt="Post-layout transient response"><br>
  <i>Fig. 6: Post-layout transient response</i>
</p>

📁 Setup and results: [Postlayout_Simulation/](./Postlayout_Simulation/)

### Results

| Parameter | Pre-layout (1.2 V) | Post-layout (1.2 V) | Post-layout (1.8 V)* |
|---|---|---|---|
| Frequency | 14.72 GHz | 10.87 GHz (−26%) | 14.41 GHz |
| Average power | 182.5 µW | 180.12 µW | 587.4 µW |
| Energy per cycle | 12.4 fJ | 16.6 fJ (+34%) | 40.8 fJ |

*\*Above the rated supply, shown for trend only.*

> [!WARNING]
> The 15 GHz target is **not met after layout** at the rated 1.2 V. Raising the supply to 1.8 V recovers most of the frequency but costs about 3.3 times the power and exceeds the rating, so it is not a valid fix.

---

## 6. PVT Analysis

All PVT plots are in [PVT_Analysis/](./PVT_Analysis/).

### Process corners (1.2 V, 27 °C)

<p align="center">
  <img src="https://github.com/user-attachments/assets/6e149695-1c8a-49ad-b713-65a646e49a50" width="700" alt="Five process corner plot"><br>
  <i>Fig. 7: Transient response in five process corners</i>
</p>

| Corner | Frequency (GHz) | vs NN | Power (µW) | Energy (fJ) |
|---|---|---|---|---|
| FF | 13.84 | +27.3% | 238.0 | 17.2 |
| NN | 10.87 | – | 180.1 | 16.6 |
| FS | 10.75 | −1.1% | 173.5 | 16.1 |
| SF | 10.01 | −7.9% | 163.5 | 16.3 |
| SS | 7.75 | −28.7% | 124.1 | 16.0 |

### Voltage (NN corner)

<p align="center">
  <img src="https://github.com/user-attachments/assets/f0fffa06-1a56-45b9-af44-6a6569eb6ba2" width="700" alt="Frequency versus supply voltage"><br>
  <i>Fig. 8: Frequency versus supply voltage</i>
</p>

| Variation | Result |
|---|---|
| Supply, 0.45 V to 1.8 V | Oscillates down to 0.45 V (about 1.6 GHz). Slope is about 9.3 GHz/V near the rated supply. |

### Temperature (NN corner)

<p align="center">
  <img src="https://github.com/user-attachments/assets/c19edf4a-c492-45af-aed6-c89dc707b563" width="700" alt="Frequency versus temperature"><br>
  <i>Fig. 9: Frequency versus temperature</i>
</p>

| Variation | Result |
|---|---|
| Temperature, 0 °C to 100 °C | Frequency falls about 27%, because carrier mobility drops at high temperature. |

- The oscillator starts in all corners, across the full supply and temperature ranges.
- Even the fast corner (FF) stays about 8% below 15 GHz, so the shortfall is systematic, not a corner effect.

---

## 7. GDS II

- The layout is DRC clean and LVS matched ([DRC/](./DRC/), [LVS/](./LVS/)), so it is the design database ready for stream-out to GDSII.
- GDSII export is not covered in the [report](./Design_of_CMOS_RO.pdf), so it is not shown here.

---

## Why the frequency dropped

- The layout adds wiring capacitance to every node, so each stage must charge a larger load and switches more slowly (compare [Fig. 3](#transient-response) with [Fig. 6](#transient-response-post-layout)).
- The estimated wiring load is about **35%** of the original load (first-order estimate).
- Power stays almost unchanged because the extra capacitance and lower frequency nearly cancel, so energy per cycle shows the real cost.
- For the model and calculations, **refer to the [report](./Design_of_CMOS_RO.pdf) (Chapter 6)**.

## Suggested Improvements

> [!IMPORTANT]
> These proposals have **not** been simulated in this project.

Layout fixes alone cannot reach 15 GHz, since the schematic is already slightly short. Both lower parasitics and speed margin are needed.

1. **Design with margin:** design the schematic above 15 GHz and confirm by re-extraction.
2. **Re-tune Wn and Wp/Wn** on the extracted circuit.
3. **Shorten the feedback wire** and place the stages tightly.
4. **Use fewer contacts and higher metal layers** to reduce added load.

---

### Repository Structure

| Folder / File | Contents |
|---|---|
| [Design_of_CMOS_RO.pdf](./Design_of_CMOS_RO.pdf) | 📄Full project report  (**start here** ). <br>It contains the theory, calculations, diagrams, waveforms and detailed analysis|
| [Schematic/](./Schematic/) | Schematic of the 3-stage ring oscillator |
| [Symbol/](./Symbol/) | Symbol (pins Vdd, Vss, Vout) |
| [Testbench/](./Testbench/) | Testbench used for simulation |
| [Prelayout_Simulation/](./Prelayout_Simulation/) | Pre-layout (schematic) simulation setup and results |
| [Layout/](./Layout/) | Full-custom layout |
| [DRC/](./DRC/) | Design rule check result |
| [LVS/](./LVS/) | Layout versus schematic result |
| [Extracted_View/](./Extracted_View/) | Quantus extraction (`av_extracted` view) |
| [Postlayout_Simulation/](./Postlayout_Simulation/) | Post-layout simulation setup and results |
| [PVT_Analysis/](./PVT_Analysis/) | Process, voltage and temperature analysis |
| [Waveforms/](./Waveforms/) | Output waveforms |

---
