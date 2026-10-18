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

> [!NOTE]
> Theory, calculations, diagrams, waveforms and detailed analysis are in the **project report**.

**Tools:** Cadence Virtuoso · Spectre · Assura / Quantus · GPDK 90 nm PDK

---

## Overview

A ring oscillator is a self-starting timing source built only from inverters, with no input signal. This project designs a **3-stage CMOS ring oscillator** in GPDK 90 nm with a **15 GHz** target.

> **In simple terms:** Every chip needs a clock that ticks billions of times per second. This project builds one from three NOT gates in a loop, then compares its speed before and after it is drawn as a real layout, where the wires slow it down.

---

## 1. Full Custom

Every transistor is sized and placed by hand, rather than generated from a standard-cell library.

- Circuit: 3-stage single-ended CMOS ring oscillator
- Technology: GPDK 90 nm
- Target frequency: 15 GHz at 27 °C

---

## 2. CMOS Design

Sizes and supply were chosen from parametric sweeps. For calculations and sweep plots, **refer to the report (Chapter 2)**.

| Parameter | Value | Why |
|---|---|---|
| Stages (N) | 3 | An odd number of inverters is required to oscillate. |
| Channel length (L) | 100 nm | Fixed by the GPDK 90 nm technology. |
| NMOS width (Wn) | 230 nm | Wn has only a small effect on frequency. |
| PMOS width (Wp) | 615 nm | Wp/Wn is set at the peak of the frequency sweep, favouring speed over symmetry. |
| Supply (Vdd) | 1.2 V | The rated supply is used so the design stays within its rating. |

The three inverters are connected in a closed loop and packaged as a symbol with pins **Vdd**, **Vss** and **Vout**. There is no input pin.

<p align="center"><i>Add image: schematic and symbol (Report Figs. 4.1, 4.2)</i></p>

---

## 3. Simulation

*Inputs: supply, temperature and device models.*

Transient analysis in Spectre at 27 °C. An initial condition (Vout = 0) starts the oscillation in the simulator; in a real circuit, noise does this.

- Frequency: **14.72 GHz** (1.9% below target)
- Average power: **182.5 µW**
- Energy per cycle: **12.4 fJ**
- Phase shift per stage: **60.2°** (ideal 60°)

<p align="center"><i>Add image: transient response (Report Figs. 4.3, 4.4)</i></p>

---

## 4. Layout

- Three identical inverters are placed side by side in the same orientation.
- The third output is routed back to the first. This feedback wire is the longest net.

<p align="center"><i>Add image: layout (Report Fig. 5.1)</i></p>

---

## 5. DRC & LVS

*Inputs: GPDK 90 nm rule files.*

- **DRC (Design Rule Check):** no errors.
- **LVS (Layout versus Schematic):** schematic and layout match.

<p align="center"><i>Add image: DRC and LVS results (Report Figs. 5.2, 5.3)</i></p>

---

## 6. Post Route Simulation

Wire resistance and capacitance were extracted with **Quantus** into the `av_extracted` view, and the simulation was repeated on it.

### Results

| Parameter | Pre-layout (1.2 V) | Post-layout (1.2 V) | Post-layout (1.8 V)* |
|---|---|---|---|
| Frequency | 14.72 GHz | 10.87 GHz (−26%) | 14.41 GHz |
| Average power | 182.5 µW | 180.12 µW | 587.4 µW |
| Energy per cycle | 12.4 fJ | 16.6 fJ (+34%) | 40.8 fJ |

*\*Above the rated supply, shown for trend only.*

> [!WARNING]
> The 15 GHz target is **not met after layout** at the rated 1.2 V. Raising the supply to 1.8 V recovers most of the frequency but costs about 3.3 times the power and exceeds the rating, so it is not a valid fix.

### Why the frequency dropped

- The layout adds wiring capacitance to every node, so each stage must charge a larger load and switches more slowly.
- The estimated wiring load is about **35%** of the original load (first-order estimate).
- Power stays almost unchanged because the extra capacitance and lower frequency nearly cancel, so energy per cycle shows the real cost.
- For the model and calculations, **refer to the report (Chapter 6)**.

### 7. PVT analysis

**Process corners (1.2 V, 27 °C)**

| Corner | Frequency (GHz) | vs NN | Power (µW) | Energy (fJ) |
|---|---|---|---|---|
| FF | 13.84 | +27.3% | 238.0 | 17.2 |
| NN | 10.87 | – | 180.1 | 16.6 |
| FS | 10.75 | −1.1% | 173.5 | 16.1 |
| SF | 10.01 | −7.9% | 163.5 | 16.3 |
| SS | 7.75 | −28.7% | 124.1 | 16.0 |

**Voltage and temperature (NN corner)**

| Variation | Result |
|---|---|
| Supply, 0.45 V to 1.8 V | Oscillates down to 0.45 V (about 1.6 GHz). Slope is about 9.3 GHz/V near the rated supply. |
| Temperature, 0 °C to 100 °C | Frequency falls about 27%, because carrier mobility drops at high temperature. |

- The oscillator starts in all corners, across the full supply and temperature ranges.
- Even the fast corner (FF) stays about 8% below 15 GHz, so the shortfall is systematic, not a corner effect.

<p align="center"><i>Add image: extracted view, post-layout waveform and PVT plots (Report Figs. 5.4 to 7.3)</i></p>

---

## 8. GDSII

- The layout is DRC clean and LVS matched, so it is the design database ready for stream-out to GDSII.
- GDSII export is not covered in the report, so it is not shown here.

---

## Suggested Improvements

> [!IMPORTANT]
> These proposals have **not** been simulated in this project.

Layout fixes alone cannot reach 15 GHz, since the schematic is already slightly short. Both lower parasitics and speed margin are needed.

1. **Design with margin:** design the schematic above 15 GHz and confirm by re-extraction.
2. **Re-tune Wn and Wp/Wn** on the extracted circuit.
3. **Shorten the feedback wire** and place the stages tightly.
4. **Use fewer contacts and higher metal layers** to reduce added load.

---

## Conclusion

- The schematic reaches 14.72 GHz, within about 2% of target. The layout is DRC and LVS clean, but wiring capacitance cuts the frequency to 10.87 GHz.
- The design is robust across process, voltage and temperature.
- **Layout effects must be planned for at the start of the design, not discovered at the end.**
