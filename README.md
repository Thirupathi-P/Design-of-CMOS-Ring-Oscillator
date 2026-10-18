<h1 align="center">Design of a CMOS Ring Oscillator</h1>

<p align="center">
  3-stage ring oscillator · GPDK 90 nm · Cadence Virtuoso · Target 15 GHz
</p>

<p align="center">
  <b>Note:</b> Please read the project report for detailed calculations, circuit diagrams, waveforms, layout and analysis.
</p>

---

A ring oscillator needs no input signal: a loop of inverters starts on its own and the frequency is set by the inverter delay.

**f = 1 / (2 · N · tp)** where N = number of stages and tp = average inverter delay.

**Why 3 stages (odd)?** Each inverter gives 180°. Three inverters give 180° in total, and the stage delays must supply the other 180° (60° per stage) for a 360° loop. An even number would latch instead of oscillating.

## Full Custom ASIC Flow (Schematic → Layout)

### 1. System Specification
3-stage ring oscillator, GPDK 90 nm, target frequency 15 GHz.

### 2. Circuit Design
Parameters were chosen from one-at-a-time parametric sweeps:

| Parameter | Observation | Final value |
|---|---|---|
| Wn | Almost no effect on frequency (larger Wn also loads the next stage) | 230 nm |
| R = Wp/Wn | Frequency rises, peaks, then falls (peak 14.48 GHz at R = 2.7) | Wp = 615 nm |
| Vdd | Frequency rises with supply but power rises faster (15 GHz at about 1.245 V) | 1.2 V |
| L, N | Fixed by technology and design choice | 100 nm, 3 |

Single-inverter delays: tpHL = 8.59 ps, tpLH = 14.15 ps (the rising edge is slower because the PMOS is weaker). Calculated frequency: **14.66 GHz**.

### 3. Schematic Entry
Built the inverter, connected three in a closed loop, and created a symbol. The testbench uses a DC supply and an initial condition (Vout = 0), because an ideal loop would otherwise stay at its unstable equilibrium point. In real silicon, noise starts the oscillation.

<p align="center"><i>📷 Add photo: schematic</i></p>

### 4. Circuit Simulation
Transient simulation in Spectre (27 °C):

| Parameter | Value |
|---|---|
| Frequency | 14.72 GHz (about 1.9% below target) |
| Average power | 182.5 µW |
| Energy per cycle | 12.4 fJ |
| Phase shift per stage | 60.2° |
| Loop gain | about 216 at DC, about 27 at the oscillation frequency |

A phase of 60° per stage and a loop gain above 1 confirm the Barkhausen criterion.

<p align="center"><i>📷 Add photo: transient waveform</i></p>

### 5. Layout Design
Three identical inverters placed side by side with the same orientation, so every stage sees the same wiring. The output of the third stage is routed back to the first.

<p align="center"><i>📷 Add photo: layout</i></p>

### 6. DRC / LVS Verification
DRC: no errors. LVS: layout matches the schematic with no net, device, pin or parameter mismatches.

### 7. Parasitic Extraction (PEX)
Extracted the wire resistance and capacitance with Quantus, giving the `av_extracted` view used for post-layout simulation.

### 8. Post-Layout Simulation

| Parameter (1.2 V) | Pre-layout | Post-layout |
|---|---|---|
| Frequency | 14.72 GHz | **10.87 GHz** (about 26% lower) |
| Average power | 182.5 µW | 180.12 µW |
| Energy per cycle | 12.4 fJ | 16.6 fJ (about 34% higher) |

Wiring capacitance slows every stage. Power barely changes because the lower frequency offsets the extra capacitance, so the energy per cycle rises. Raising Vdd to 1.8 V recovers 14.41 GHz but costs 587.4 µW (about 3.3× the power) and exceeds the rated supply, so the target is not met after layout at any valid supply.

<p align="center"><i>📷 Add photo: post-layout waveform</i></p>

### 9. PVT Analysis
Run on the post-layout circuit.

**Process corners (1.2 V, 27 °C)**

| Corner | FF | NN | FS | SF | SS |
|---|---|---|---|---|---|
| Frequency (GHz) | 13.84 | 10.87 | 10.75 | 10.01 | 7.75 |
| Power (µW) | 238.0 | 180.1 | 173.5 | 163.5 | 124.1 |

| Variation | Result |
|---|---|
| Process | Oscillates in all 5 corners, −28.7% (SS) to +27.3% (FF) around NN |
| Voltage (27 °C) | Oscillates down to 0.45 V (about 1.6 GHz); about 9.3 GHz/V near 1.2 V |
| Temperature (1.2 V) | Falls about 27% from 0 °C to 100 °C (carrier mobility drops with temperature) |

<p align="center"><i>📷 Add photo: corner, voltage and temperature plots</i></p>

## Key Learnings

- Layout parasitics cost about 26% of the frequency, so consider them early in the design.
- Supply voltage has the largest effect on both frequency and power.
- Shorter routing, tighter placement or re-tuned device sizes can recover the lost frequency within the rated supply.
