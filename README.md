<h1 align="center">Design of CMOS Ring Oscillator</h1>

<p align="center">
  3-Stage Ring Oscillator · GPDK 90 nm Technology · Cadence Virtuoso · Target Frequency 15 GHz
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Target-15%20GHz-blue" alt="Target">
  <img src="https://img.shields.io/badge/Pre--layout-14.72%20GHz-brightgreen" alt="Pre-layout">
  <img src="https://img.shields.io/badge/Post--layout-10.87%20GHz-red" alt="Post-layout">
  <img src="https://img.shields.io/badge/DRC%20%2F%20LVS-passed-brightgreen" alt="DRC LVS">
  <img src="https://img.shields.io/badge/Technology-GPDK%2090%20nm-orange" alt="Technology">
</p>

> [!NOTE]
> Please read the project report for detailed calculations, circuit diagrams, simulation waveforms, layout and analysis.

---

## Abstract

Ring oscillators are widely used on chip as compact, self-starting sources of timing signals, since they need no input signal and are built only from inverters. This project presents the design of a 3-stage CMOS ring oscillator in the GPDK 90 nm technology using Cadence Virtuoso, with a target oscillation frequency of 15 GHz.

The design follows the full-custom ASIC flow:

`System Specification → Circuit Design → Schematic Entry → Simulation → Layout → DRC / LVS → Parasitic Extraction → Post-Layout Simulation → PVT Analysis`

> **In simple terms:** Every digital chip needs a clock, a signal that ticks billions of times per second to keep its parts in step. This project designs a simple clock circuit from three NOT gates connected in a loop, and checks how well it performs before and after it is drawn as a real chip layout.

### Key Results

| Parameter | Target | Pre-layout | Post-layout (1.2 V) |
|---|---|---|---|
| Frequency | 15 GHz | 14.72 GHz | 10.87 GHz |
| Average power | – | 182.5 µW | 180.12 µW |
| Energy per cycle | – | 12.4 fJ | 16.6 fJ |
| DRC / LVS | – | – | Passed, no errors |

---

## 1. Ring Oscillator

A ring oscillator is a chain of an odd number of inverters connected in a closed loop. The output of the last inverter is fed back to the input of the first, which creates a regenerative delay loop that sustains oscillation without any input signal.

> **In simple terms:** A NOT gate (inverter) flips a signal. With an odd number of gates in a circle, the signal never settles and keeps flipping, which produces a continuous tick.

<p align="center"><i>Add image: block diagram and transistor-level schematic</i></p>

### 1.1 Oscillation Frequency

The period of the output is twice the sum of the stage delays:

**f = 1 / (2 · N · tp) = 1 / (N · (tpHL + tpLH))**

where N is the number of stages, tp is the average propagation delay of one inverter, tpHL is the high-to-low delay and tpLH is the low-to-high delay.

> **In simple terms:** The faster each gate responds, the faster the circuit ticks. Fewer gates also tick faster, but with less stable waveforms.

### 1.2 Barkhausen Criterion

For sustained oscillation, two conditions must hold:

1. The loop gain must be at least unity.
2. The total phase shift around the loop must be a multiple of 360°.

Each inverter provides 180° of inversion. With N = 3, the three inverters give a net 180° inversion, and the stage delays supply the remaining 180°, which is 180° / 3 = **60° per stage**. An even number of inverters would latch instead of oscillating, which is why N must be odd.

---

## 2. Design of Ring Oscillator

### 2.1 Design Parameters and Specification

The frequency depends on f(R, Vdd, L, N), where R = Wp / Wn is the PMOS-to-NMOS width ratio. The number of stages (N = 3) and channel length (L = 100 nm) are fixed by the design choice and the technology. The NMOS width, the ratio R and the supply voltage were selected from parametric sweeps.

### 2.2 Design Calculations

Each parameter was varied one at a time
