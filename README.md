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
> Theory, derivations, calculations, circuit diagrams, simulation waveforms, layout and detailed analysis are in the **project report**. This README summarises the work and the key results.

---

## Tools Used

Cadence Virtuoso · Spectre · Assura / Quantus · GPDK 90 nm PDK

---

## Overview

A ring oscillator is a compact, self-starting timing source built only from inverters, with no input signal required. This project designs a **3-stage CMOS ring oscillator** in **GPDK 90 nm** using **Cadence Virtuoso**, with a target frequency of **15 GHz**.

The design is taken through the complete full-custom flow: sizing, schematic, simulation, layout, DRC/LVS, parasitic extraction, post-layout simulation and PVT analysis. The project measures what layout does to the design, quantifies it and explains it. It does not stop at the ideal schematic result.

> **In simple terms:** Every digital chip needs a clock, a signal that ticks billions of times per second. This project builds a clock from three NOT gates connected in a loop. It then compares the speed on paper with the speed after the circuit is drawn as a real chip layout, where the wires slow it down.

---

## Highlights

- Pre-layout frequency of **14.72 GHz**, within 1.9% of the 15 GHz target.
- Layout is **DRC clean and LVS matched**.
- Post-layout frequency of **10.87 GHz**, a **26% drop**, traced to wiring capacitance.
- Post-layout **PVT analysis**: 5 process corners, supply sweep from 0.45 V to 1.8 V, temperature sweep from 0 °C to 100 °C.
- The 15 GHz target is **not met after layout** at the rated supply. This is reported openly, with the cause and a plan to fix it.

---

## 1. System Specification

- Circuit: 3-stage single-ended CMOS ring oscillator
- Technology: GPDK 90 nm
- Target frequency: 15 GHz
- Supply voltage: 1.2 V (rated)
- Temperature: 27 °C (nominal)

---

## 2. Circuit Design

Transistor sizes and supply were chosen from one-at-a-time parametric sweeps of the oscillation frequency. For the design calculations and sweep plots, **refer to the report (Chapter 2)**.

**Final design**

- Number of stages (N): 3
- NMOS width (Wn): 230 nm
- PMOS width (Wp): 615 nm
- Channel length (L): 100 nm
- Supply voltage (Vdd): 1.2 V

**How each parameter was chosen**

- **N = 3:** A ring needs an odd number of inverting stages to oscillate. Three is the fastest odd choice.
- **L = 100 nm:** Fixed by the GPDK 90 nm technology for all transistors.
- **Wn:** The sweep shows Wn has only a small effect on frequency.
- **Wp / Wn ratio (R):** Chosen at the peak of the frequency sweep. A larger PMOS gives more drive current but also loads the previous stage.
- **Vdd = 1.2 V:** The sweep reaches 15 GHz slightly above 1.2 V. The rated 1.2 V was used so the design stays within the supply rating.

**Important note on R**

- R was chosen for **maximum frequency, not for symmetry**.
- As a result, the output rises more slowly than it falls.

---

## 3. Schematic Entry

- Three identical CMOS inverters are connected in a closed loop, with the output of the third fed back to the first.
- The circuit is packaged as a symbol with three pins: **Vdd**, **Vss** and **Vout**.
- There is no input pin, because the oscillator generates its own signal.

<p align="center"><i>Add image: transistor-level schematic and symbol (Report Figs. 4.1, 4.2)</i></p>

---

## 4. Pre-Layout Simulation

### Simulation setup

- Transient analysis in Spectre at 27 °C.
- Start-up: in an ideal simulation the loop would sit at its unstable equilibrium, so an initial condition **Vout = 0** is applied (ADE-L: Simulation → Convergence Aids → Initial Condition). In a real circuit, noise starts the oscillation.

### Results

- Frequency: **14.72 GHz** (1.9% below target)
- Frequency settles to within 0.1% shortly after start-up.
- Average power: **182.5 µW**
- Energy per cycle: **12.4 fJ**
- Phase shift per stage: **60.2°** (ideal 60°)

---

## 5. Layout Design

- Three identical inverters are placed side by side in the same orientation, so the stages see nearly the same wiring and mismatch is small.
- The output of the third stage is routed back to the input of the first.
- The feedback wire is the longest net in the layout, so it is the main point of asymmetry and a likely major source of parasitic capacitance.

<p align="center"><i>Add image: layout (Report Fig. 5.1)</i></p>

---

## 6. Physical Verification (DRC / LVS)

- **DRC (Design Rule Check):** checks the width, spacing and enclosure rules of the process. Result: **no errors**.
- **LVS (Layout versus Schematic):** checks that the layout is electrically the same circuit as the schematic. Result: **Schematic and Layout Match**, with no net, device, pin or parameter mismatches.

<p align="center"><i>Add image: DRC and LVS results (Report Figs. 5.2, 5.3)</i></p>

---

## 7. Parasitic Extraction (PEX)

- Wire and contact resistance and capacitance were extracted with **Quantus**.
- The result is the `av_extracted` view, used for post-layout simulation.

> **In simple terms:** Real wires add small unwanted electrical effects. This step measures them so the next simulation reflects the real chip.

<p align="center"><i>Add image: extraction run and extracted view (Report Figs. 5.4, 5.5)</i></p>

---

## 8. Post-Layout Simulation

The same test is repeated on the extracted view at 27 °C.

- At **1.2 V (rated):** 10.87 GHz, 180.12 µW, 16.6 fJ per cycle
- At **1.8 V (above rating, trend only):** 14.41 GHz, 587.4 µW, 40.8 fJ per cycle

> [!WARNING]
> The 15 GHz target is **not met after layout** at the rated 1.2 V supply. Raising the supply to 1.8 V recovers 14.41 GHz but costs about 3.3 times the power and exceeds the rated voltage, so it is not a valid fix.

<p align="center"><i>Add image: post-layout waveform and measurements (Report Figs. 6.1 to 6.3)</i></p>

---

## 9. PVT Analysis

PVT checks the post-layout oscillator under variations in **P**rocess, **V**oltage and **T**emperature.

### Process corners (1.2 V, 27 °C)

| Corner | Frequency (GHz) | vs NN | Power (µW) | Energy (fJ) |
|---|---|---|---|---|
| FF | 13.84 | +27.3% | 238.0 | 17.2 |
| NN | 10.87 | – | 180.1 | 16.6 |
| FS | 10.75 | −1.1% | 173.5 | 16.1 |
| SF | 10.01 | −7.9% | 163.5 | 16.3 |
| SS | 7.75 | −28.7% | 124.1 | 16.0 |

*FF = fast, SS = slow, NN = nominal. FS and SF have one transistor type fast and the other slow.*

- The oscillator starts in all five corners and stays within the 0 to 1.2 V rails.
- Even the fastest corner (FF) is about 8% below 15 GHz, so the shortfall is systematic and not a corner effect.
- Energy per cycle varies only from 16.0 to 17.2 fJ, because power and frequency move together.

### Voltage (27 °C, NN corner, 0.45 V to 1.8 V)

- The oscillator keeps running down to **0.45 V** (about 1.6 GHz).
- Frequency is about 7.8 GHz at 0.9 V, 12.0 GHz at 1.35 V and 14.4 GHz at 1.8 V.
- The slope is about **9.3 GHz/V** from 0.9 to 1.35 V, and lower above 1.35 V, because gate overdrive grows more slowly at high supply.
- Near 1.2 V, a ±10% supply change shifts the frequency by about ±10%.

### Temperature (1.2 V, NN corner, 0 °C to 100 °C)

- Frequency falls almost linearly: about 11.9 GHz at 0 °C, 10.87 GHz at 27 °C and 8.7 GHz at 100 °C.
- This is a drop of about **27%** over the range.
- Cause: carrier mobility decreases at high temperature, which reduces the drive current of both transistors.

---

## Results

Comparison at 1.2 V and 27 °C:

| Parameter | Pre-layout | Post-layout |
|---|---|---|
| Frequency | 14.72 GHz | 10.87 GHz (−26%) |
| Average power | 182.5 µW | 180.12 µW (almost unchanged) |
| Energy per cycle | 12.4 fJ | 16.6 fJ (+34%) |

---

## Why Did the Frequency Drop After Layout?

**Short answer:** the layout adds wiring capacitance to every node, so each stage takes longer to switch.

- In the schematic, each node has only the capacitance of the transistors.
- In the layout, metal wires and contacts add extra capacitance to every connection.
- The inverters have the same drive strength but a larger load, so stage delay rises and the ring frequency falls.
- The measured drop is about **26%**.
- The estimated wiring load is about **35%** of the original load (first-order estimate).
- For the model and calculation, **refer to the report (Chapter 6)**.

**Why power did not change**

- The extra capacitance and the lower frequency nearly cancel each other.
- For the power relation, **refer to the report (Chapter 6)**.

---

## Suggested Improvements

> [!IMPORTANT]
> These are engineering proposals based on the analysis above. They have **not** been simulated in this project.

**Key point:** layout optimisation alone cannot reach 15 GHz. The schematic is already slightly short of the target, so a complete fix needs both parasitic reduction and extra speed margin before layout.

1. **Design with margin**
   - Since a large share of frequency is lost in layout, the schematic should be designed above 15 GHz.
   - The margin must be confirmed by re-extraction. For the estimate, **refer to the report**.
2. **Re-tune sizes on the extracted circuit**
   - Optimise Wn and R using the post-layout netlist instead of the schematic.
   - Moderately larger transistors, with R kept near its current value, may make the fixed wire load a smaller share of the total load.
3. **Shorten the feedback wire and place the stages tightly**
   - The feedback net is the longest, so it is the first target.
   - Equal-length connections between stages remove the asymmetry.
4. **Reduce contacts and use higher metal layers**
   - Fewer and smaller contacts reduce added load.
   - Upper metal layers are generally farther from the silicon and add less capacitance.

**Not recommended**

- Raising the supply above 1.2 V. The 1.8 V result shows the frequency recovery, but it exceeds the rating and costs about 3.3 times the power.

**How the improvements would be verified**

- Update the layout, then re-run DRC, LVS and PEX.
- Re-simulate at 1.2 V and compare frequency, power and energy per cycle with the values above.
- Repeat the corner, supply and temperature sweeps to confirm the gain holds across PVT.

---

## Conclusion

- The schematic reaches **14.72 GHz**, within about 2% of the 15 GHz target, with 182.5 µW and 12.4 fJ per cycle.
- The layout passes **DRC and LVS**. After extraction, the frequency falls to **10.87 GHz (−26%)** because of wiring capacitance.
- The post-layout oscillator starts in all five process corners and down to 0.45 V, and loses about 27% of its frequency from 0 °C to 100 °C.
- Closing the gap needs both lower parasitics and design margin before layout.
- Main lesson: **layout effects must be planned for at the start of the design, not discovered at the end.**
