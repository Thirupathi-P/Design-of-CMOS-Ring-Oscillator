<h1 align="center">Design of a CMOS Ring Oscillator</h1>

<p align="center">
  A tiny on-chip "clock" circuit · 90 nm chip technology · Cadence Virtuoso
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Target-15%20GHz-blue" alt="Target">
  <img src="https://img.shields.io/badge/Before%20layout-14.72%20GHz-brightgreen" alt="Pre-layout">
  <img src="https://img.shields.io/badge/After%20layout-10.87%20GHz-red" alt="Post-layout">
  <img src="https://img.shields.io/badge/DRC%20%2F%20LVS-passed-brightgreen" alt="DRC LVS">
  <img src="https://img.shields.io/badge/Technology-GPDK%2090%20nm-orange" alt="Technology">
</p>

> [!NOTE]
> Please read the project report for detailed calculations, circuit diagrams, waveforms, layout and analysis.

---

**What is this project?**
Every digital chip needs a clock, a signal that ticks millions or billions of times per second to keep everything in step. A *ring oscillator* is one of the simplest ways to make that tick. It is just three NOT gates (called inverters) connected in a circle. The output of the last gate feeds back into the first, so the circuit keeps flipping on its own, with no input needed.

**How fast does it tick?**
Each gate takes a tiny amount of time to respond. The faster the gates, the faster the ticking.

**Frequency = 1 / (2 × number of gates × delay per gate)**

**Why three gates?**
A NOT gate flips a signal. An odd number of flips means the signal comes back flipped, so the circuit never settles and keeps switching. With an even number, it would get stuck.

> [!TIP]
> **Goal:** build this circuit as a real chip design that ticks **15 billion times per second (15 GHz)**.

## Full Custom ASIC Flow (Schematic → Layout)

### 1. System Specification
Decide what to build: a 3-gate ring oscillator on a 90 nm chip technology, ticking at 15 GHz.

### 2. Circuit Design
Each gate is made of two transistors (tiny electronic switches). I tested different sizes and supply voltages to see what gives the best speed:

| What I changed | What I found | Final choice |
|---|---|---|
| Size of the NMOS transistor (Wn) | 🔵 Almost no effect on speed | 230 nm |
| Size ratio of PMOS to NMOS (R) | 🟢 Speed rises, peaks, then falls; best at R = 2.7 | PMOS = 615 nm |
| Supply voltage (Vdd) | 🟡 Higher voltage is faster, but uses much more power | 1.2 V |
| Gate length (L) and number of gates (N) | 🔵 Set by the technology and the design | 100 nm, 3 gates |

I also measured how long one gate takes to switch: 8.59 ps for the falling edge and 14.15 ps for the rising edge (1 ps = one trillionth of a second). Using the formula above, the expected speed is **14.66 GHz**.

### 3. Schematic Entry
Drew the circuit diagram on the computer: one gate, then three gates connected in a circle. In the simulator, I gave the circuit a small starting push (output = 0), because a perfectly ideal circuit would just sit still. In a real chip, tiny electrical noise starts it automatically.

<p align="center"><i>📷 Add photo: schematic</i></p>

### 4. Circuit Simulation
Ran a computer simulation of the circuit (at 27 °C, room temperature):

| What was measured | Result | What it means |
|---|---|---|
| Speed (frequency) | 🟢 14.72 GHz | Within about 2% of the 15 GHz goal |
| Average power | 🟢 182.5 µW | Very small power use |
| Energy per tick | 🔵 12.4 fJ | Energy used for one full cycle |
| Timing delay per gate (phase) | 🟢 60.2° | Matches the expected 60°, so the circuit works as theory predicts |

<p align="center"><i>📷 Add photo: output waveform</i></p>

### 5. Layout Design
Turned the diagram into the actual physical drawing of the chip: the shapes and wires that would be made in a factory. The three gates are identical and placed side by side, so each one behaves the same. The last gate's output is wired back to the first.

<p align="center"><i>📷 Add photo: layout</i></p>

### 6. DRC / LVS Verification
Two safety checks on the drawing:
- 🟢 **DRC** checks the factory's drawing rules (for example, minimum wire width and spacing). **No errors.**
- 🟢 **LVS** checks that the drawing matches the original circuit diagram. **Perfect match.**

### 7. Parasitic Extraction (PEX)
Real wires are not perfect. Each wire adds a little extra resistance and stores a little extra electrical charge (capacitance), which slows the circuit down. This step measures those extras from the drawing, so they can be included in the next simulation.

### 8. Post-Layout Simulation
Simulated again, now including the wire effects:

| What was measured (at 1.2 V) | Before layout | After layout |
|---|---|---|
| Speed (frequency) | 🟢 14.72 GHz | 🔴 **10.87 GHz** (about 26% slower) |
| Average power | 🔵 182.5 µW | 🔵 180.12 µW |
| Energy per tick | 🟢 12.4 fJ | 🟡 16.6 fJ (about 34% more) |

> [!WARNING]
> The wires slowed the circuit down, so the 15 GHz goal was
