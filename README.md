# High-Reliability 3.3V Power Supply for STM32

## Notice
* **Note:** The theoretical explanations inside the attached report may not be 100% accurate, but the LTspice simulations and physical hardware experiments work perfectly. 

## Overview
This repository contains the design, simulation, and hardware implementation of a highly reliable 3.3V power supply module optimized for the **STM32L432K** microcontroller. 

The circuit utilizes an **LM317** linear voltage regulator. It focuses on delivering a precise voltage output while ensuring long-term hardware safety, high noise immunity, and stability under dynamic active loads (simulating STM32 clock switching at 1 MHz and 80 MHz).

## Key Specifications
* **Input Voltage Range:** 6V to 8V nominal (Tested safe up to $\pm$16V).
* **Output Voltage:** 3.318V (Target: 3.3V, operating safely within the 1.71V - 4.0V MCU limit).
* **Output Current:** > 140 mA (DC fixed load) / > 10 mA (Active switching load).
* **Voltage Ripple:** **1.496%** (Significantly below the 5% maximum constraint).
* **Current Ripple (Active Load):** **1.155%** @ 1 MHz / **1.540%** @ 80 MHz (Well below the 5% and 10% maximums).
* **Manufacturing Cost:** < $5.00 AUD per unit (10,000 units/year scale) using standard E3 series components.

## System Architecture & Protections
The power supply is designed not just for regulation, but for extreme durability against user error and electrical noise.

1. **Core Regulation:** Uses the LM317 regulator with a standard E3 resistor divider network ($R1 = 220\Omega$, $R2 = 364\Omega$) to set the 1.25V reference to a stable 3.318V output.
2. **Noise & Ripple Filtering Network:** - $C1 = 1\mu F$: Placed at the ADJ pin to reduce high-frequency input to ground and prevent amplification by the LM317.
   - $C2 = 47\mu F$: Placed at the input to filter source noise and transient spikes traveling back to the input side.
   - $C3 = 1\mu F$: Placed at the output to stabilize the voltage against the active load switching of the MCU.
3. **Reverse Polarity Protection:** A `1N4007` power diode is placed at the input rail, completely blocking current and preventing thermal failure if the power supply is connected backwards.

## Known Limitations & Engineering Disclaimers
* **OVP (Overvoltage Protection) Flaw:** The 3.6V Zener diode is placed directly in parallel with the load without a series current-limiting resistor. While this clamps voltage successfully in ideal LTspice simulations, in a real-world catastrophic LM317 failure (e.g., input shorted to output), the Zener would draw excessive current, exceed its power rating, and burn out almost instantly. Implementing a true hardware protection scheme would require a Crowbar circuit (Zener + Fuse), as adding a simple series resistor would ruin the voltage regulation under variable loads.
* **LM317 Protection:** The LM317 regulator itself does not have dedicated external protection against severe thermal overload or sustained short circuits in this specific cost-constrained BOM.
* **Theoretical vs. Empirical Results:** While the theoretical derivations in the report may contain idealizations, the LTspice simulations and physical hardware experiments (oscilloscope measurements) strictly validate the operational stability of the capacitor filtering network under normal conditions.

## Simulation & Testing Methodology
The circuit was mathematically modeled and extensively tested using **LTspice** before physical prototyping on a veroboard:
* **DC Fixed Load Testing:** Simulated using a 10$\Omega$ resistor and a continuously-ON `VN2222LL` MOSFET to draw 238mA and verify steady-state thermal and voltage stability.
* **Active Load Testing (Transient):** The `VN2222LL` MOSFET was driven by high-frequency pulses (1 MHz and 80 MHz) to mimic the rapid current draw of the STM32's internal clocking. 
* **Noise Injection:** A 100kHz sinusoidal AC component was superimposed on the DC supply in simulations to verify the filtering capability of the capacitor network.

## Repository Contents
* `/Simulations/` - LTspice `.asc` circuit files for DC load, Active load, and Fault condition testing.
* `/Hardware/` - Schematic diagrams and physical Veroboard layouts.
* `Report.pdf` - The full 17-page engineering design report containing all formulas, component justifications, and DSO oscilloscope measurements.

## Circuit Schematic
![Circuit Schematic](circuit.jpg)

## Demo 
* If you would like to see a demo video of the hardware in action, please let me know!

## Alternative Version (V2)
* `circuit_V2` is also included in this repository. Please note that while this version does not have an accompanying written report, its functionality has been fully verified through prior LTspice simulations and physical experiments.
