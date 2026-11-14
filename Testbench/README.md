# Testbench

**Inverter testbench** (pulse 0 to 1.2 V, tr = 1 ns, Vdd = 1.2 V, tran 0 to 200 ns)
![Inverter TB](Fig3.1_Inverter_Testbench.png)

**Inverter ADE setup and measured delays**
![Inverter ADE](Fig3.3_Inverter_ADE_Setup_Propagation_Delay.png)

| Parameter | Value |
|---|---|
| tpHL | 8.589 ps |
| tpLH | 14.154 ps |
| tp = (tpHL + tpLH)/2 | 11.37 ps |
| f = 1/(2 N tp), N = 3 | 14.66 GHz |

**Ring oscillator ADE setup (pre-layout)**
![RO ADE](Fig4.4_RO_ADE_Setup_Frequency_Power.png)

The initial condition Vout = 0 is set under ADE-L → Simulation → Convergence Aids → Initial Condition to start the oscillation.
