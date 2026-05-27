# High-Performance Analog Front-End (AFE) Input Stage with Active Right-Leg Drive (RLD)

This repository contains the design, simulation, and verification of a medical-grade Analog Front-End (AFE) input stage optimized for biopotential signal acquisition (such as EEG/ECG). The circuit features a high-input-impedance instrumentation amplifier topology paired with an active Right-Leg Drive (RLD) loop to actively suppress common-mode noise and powerline interference ($50\text{ Hz}/60\text{ Hz}$ hum).

Simulations were executed using precision macromodels within **LTspice** to validate gain flatness, phase response, and active safety feedback loops.

---

## Technical Specifications & Architecture

The architecture is built across three core stages using low-noise, precision operational amplifiers (**LTC6078**):

1. **High-Impedance Input Buffer Stage ($U_1, U_2$):** Prevents signal attenuation caused by electrode-skin mismatch. The differential gain is established using a fixed feedback network ($R_{f1} = R_{f2} = 20\text{ k}\Omega$) and an adjustable gain resistor ($R_G = 2\text{ k}\Omega$).
2. **Precision Difference Amplifier Stage ($U_3$):** Subtracts the buffered input paths using tightly matched $100\text{ k}\Omega$ resistors ($R_1, R_2, R_3, R_4$) to perform common-mode rejection.
3. **Active Right-Leg Drive Loop ($U_4$):** Samples the common-mode voltage ($V_{CM}$) via a summing network ($R_5 = R_6 = 100\text{ k}\Omega$), inverts the noise signal, and drives it back to the body reference point through a current-limiting safety resistor ($R_8 = 100\text{ k}\Omega$) for active noise cancellation.

### Mathematical Formulation

The theoretical mid-band differential voltage gain ($A_v$) of the instrumentation stage is derived as:

$$A_v = \left(1 + \frac{2 \cdot R_{f1}}{R_G}\right) \cdot \left(\frac{R_3}{R_1}\right)$$

Substituting the chosen design parameters:

$$A_v = \left(1 + \frac{2 \cdot 20\text{ k}\Omega}{2\text{ k}\Omega}\right) \cdot \left(\frac{100\text{ k}\Omega}{100\text{ k}\Omega}\right) = 21 \text{ V/V}$$

Converting the voltage gain to decibels:

$$A_{v(\text{dB})} = 20 \cdot \log_{10}(21) \approx 26.44 \text{ dB}$$

---

## Schematic Capture

The complete circuit layout was constructed with optimized spatial routing to isolate the sensitive microvolt-level biopotential inputs from the power supply lines.

![Biomedical AFE Schematic](images/afe_schematic.png)

---

## Simulation Results & Performance Verification

An AC frequency sweep analysis (`.ac dec 100 0.1 10k`) was conducted to evaluate the response of the instrumentation amplifier from $0.1\text{ Hz}$ to $10\text{ kHz}$.

### 1. Differential Passband Gain
Probing the output node of $U_3$ confirms a flawless, ultra-flat differential gain response matching the mathematical expectation at **$+26.46\text{ dB}$**. The gain variation across the medical passband is less than $0.03\text{ dB}$, ensuring zero distortion of the biological wave frequencies.

![AC Gain Response](images/gain_response.png)

### 2. RLD Error Stabilization
Probing the output of the active common-mode cancellation feedback network (`RLD_OUT`) shows deep attenuation under differential excitation (sitting below **$-140\text{ dB}$**), verifying that the inverting error amplifier is stable, well-compensated by the parallel $1.5\text{ nF}$ and $1\text{ M}\Omega$ network, and completely free of self-oscillation.

---

## How to Run the Simulation

1. Download the schematic file `eeg-afe-project.asc` from the root directory.
2. Open the file inside **LTspice**.
3. Click the **Run** (Green Running Man icon) toolbar button to assemble the netlist.
4. To view the amplified signal response, click the voltage probe tool on the output pin of **U3** or the net labeled `Vout`.
5. To view the active shield behavior, probe the node labeled `RLD_OUT`.

---

## Repository Structure

```text
eeg-afe-input-stage-simulator/
│
├── eeg-afe-project.asc         # Main LTspice schematic blueprint file
├── README.md                   # Project documentation and performance summary
└── images/                     # Embedded high-resolution simulation graphics
    ├── afe_schematic.png       # Schematic circuit capture diagram
    └── gain_response.png       # Frequency response magnitude & phase plot
