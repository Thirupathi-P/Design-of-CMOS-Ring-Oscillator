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
> Please read the project report for detailed theory, design calculations, circuit diagrams, simulation waveforms, layout and analysis.

---

## Overview

Ring oscillators are compact, self-starting sources of timing signals, built only from inverters and needing no input signal. This project designs a 3-stage CMOS ring oscillator in the GPDK 90 nm technology using Cadence Virtuoso, with a target oscillation frequency of **15 GHz**.

> **In simple terms:** Every digital chip needs a clock, a signal that ticks billions of times per second to keep its parts in step. This project builds a simple clock from three NOT gates connected in a loop, and checks how it performs before and after it is drawn as a real chip layout.

### Key Results

| Parameter | Target | Pre-layout | Post-layout (1.2 V) |
|---|---|---|---|
| Frequency | 15 GHz | 14.72 GHz | 10.87 GHz |
| Average power | – | 182.5 µW | 180.12 µW |
| Energy per cycle | – | 12.4 fJ | 16.6 fJ |
| DRC / LVS | – | – | Passed, no errors |

---

## Full Custom ASIC Flow (Schematic → Layout)

### 1. System Specification

Design a 3-stage CMOS ring oscillator in GPDK 90 nm with a target frequency of 15 GHz.

### 2. Circuit Design

The transistor sizes and supply voltage were selected from parametric simulation sweeps. For the theory, design calculations and sweep plots, **refer to the report**.

| Parameter | Value |
|---|---|
| Number of stages (N) | 3 |
| NMOS width (Wn) | 230 nm |
| PMOS width (Wp) | 615 nm |
| Channel length (L) | 100 nm |
| Supply voltage (Vdd) | 1.2 V |

### 3. Schematic Entry

Three CMOS inverters were connected in a closed loop in Cadence Virtuoso and packaged as a symbol with three pins: Vdd, Vss and Vout. The symbol has no input pin, since the oscillator generates its own signal.

<p align="center"><i>Add image: schematic and symbol</i></p>

### 4. Circuit Simulation

Transient simulation in Spectre at 27 °C. An initial condition (Vout = 0) is applied to start the oscillation in the simulator. In a real circuit, noise starts it.

| Parameter | Value |
|---|---|
| Frequency | 14.72 GHz (about 1.9% below target) |
| Average power | 182.5 µW |
| Energy per cycle | 12.4 fJ |
| Phase shift per stage | 60.2° (expected 60°) |

The measured values agree with the hand calculation (14.66 GHz). **Refer to the report** for the delay measurements, calculation and loop-gain analysis.

<p align="center"><i>Add image: transient response</i></p>

### 5. Layout Design

Three identical inverters are placed side by side with the same orientation, so every stage sees the same wiring and mismatch is small. The output of the third stage is routed back to the first.

<p align="center"><i>Add image: layout</i></p>

### 6. DRC / LVS Verification

| Check | Purpose | Result |
|---|---|---|
| DRC (Design Rule Check) | Verifies the width, spacing and enclosure rules of the process | No errors |
| LVS (Layout versus Schematic) | Verifies that the layout matches the schematic | Match, no mismatches |

<p align="center"><i>Add image: DRC and LVS results</i></p>

### 7. Parasitic Extraction (PEX)

The resistance and capacitance of the wires and contacts were extracted using Quantus, producing the `av_extracted` view for post-layout simulation.

> **In simple terms:** Real wires add small unwanted electrical effects. This step measures them so they can be included in the next simulation.

<p align="center"><i>Add image: extracted view</i></p>

### 8. Post-Layout Simulation

The simulation was repeated on the extracted view (27 °C).

| Parameter | Pre-layout (1.2 V) | Post-layout (1.2 V) | Post-layout (1.8 V) |
|---|---|---|---|
| Frequency | 14.72 GHz | 10.87 GHz | 14.41 GHz |
| Average power | 182.5 µW | 180.12 µW | 587.4 µW |
| Energy per cycle | 12.4 fJ | 16.6 fJ | 40.8 fJ |

The 1.8 V column is above the rated supply and is shown for trend only.

> [!WARNING]
> The 15 GHz target is **not met after layout** at the rated 1.2 V supply. Raising the supply to 1.8 V recovers 14.41 GHz but costs about 3.3 times the power.

<p align="center"><i>Add image: post-layout waveform</i></p>

### 9. PVT Analysis

PVT analysis checks the post-layout oscillator under variations in Process, Voltage and Temperature.

**Process corners (1.2 V, 27 °C)**

| Corner | Frequency (GHz) | vs NN | Power (µW) | Energy (fJ) |
|---|---|---|---|---|
| FF | 13.84 | +27.3% | 238.0 | 17.2 |
| NN | 10.87 | – | 180.1 | 16.6 |
| FS | 10.75 | −1.1% | 173.5 | 16.1 |
| SF | 10.01 | −7.9% | 163.5 | 16.3 |
| SS | 7.75 | −28.7% | 124.1 | 16.0 |

FF = fast, SS = slow, NN = nominal, FS and SF = one transistor type fast and the other slow.

| Variation | Result |
|---|---|
| Process | Oscillates in all 5 corners; even FF stays about 8% below the 15 GHz target |
| Voltage (27 °C) | Oscillates down to 0.45 V (about 1.6 GHz); slope about 9.3 GHz/V near 1.2 V |
| Temperature (1.2 V) | Frequency falls about 27% from 0 °C to 100 °C, because carrier mobility drops at high temperature |

<p align="center"><i>Add image: process-corner, supply and temperature plots</i></p>

---

## Why Did the Frequency Drop After Layout?

**Exact reason: the layout adds wiring capacitance to every node.**

In the schematic, each inverter drives only the next inverter's transistors. In the layout, the metal wires and contacts between the stages add extra capacitance. The inverters have the same drive strength but a larger load to charge and discharge, so each stage switches more slowly. Since the ring frequency depends only on the stage delay, it falls.

**f(post) / f(pre) = 1 / (1 + Cpar / C)**

The measured ratio is 10.87 / 14.72 ≈ 0.74, so the frequency fell by about 26%. This corresponds to a wiring load of about 35% of the original load (a first-order estimate). The power stays almost the same because the lower frequency offsets the extra capacitance, so the energy per cycle rises from 12.4 fJ to 16.6 fJ.

## Suggested Improvements

> [!IMPORTANT]
> These are design suggestions based on the analysis above. They have **not** been simulated in this project.

| Solution | How it helps |
|---|---|
| Shorter wires and tighter placement | Less wire gives less extra load. The feedback wire from the last stage to the first is the best place to start. |
| Fewer and smaller contacts | Fewer connection points give less extra load |
| Route on higher metal layers | Upper layers are generally farther from the silicon, so they add less capacitance |
| Moderately larger transistors (keep R = 2.7) | The wire load becomes a smaller fraction of the total load |
| Re-tune Wn and R on the extracted circuit | Optimises the sizes using real layout parasitics, not only the schematic |
| Design with margin | Since about 26% is lost in layout, aim higher before layout to land near 15 GHz after |

A higher supply voltage is **not** a safe solution, because 1.8 V is above the rated 1.2 V.

---

## Conclusion

- The schematic reaches 14.72 GHz, within about 2% of the 15 GHz target.
- The layout passes DRC and LVS. After extraction, the frequency falls to 10.87 GHz (−26%) because of wiring capacitance.
- The post-layout oscillator starts in all five process corners and down to a 0.45 V supply, and loses about 27% of its frequency from 0 °C to 100 °C.
- Layout effects should be considered early in the design, and shorter routes, tighter placement or re-tuned device sizes can recover the lost frequency within the rated supply.
