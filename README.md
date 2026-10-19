# ECG Analog Front-End Design Using LTspice

An analog ECG signal conditioning circuit designed and simulated in LTspice. The project combines a three-op-amp instrumentation amplifier, a Right Leg Drive (RLD) feedback circuit, and a 50 Hz Twin-T notch filter to amplify low-level ECG signals and reduce common-mode and power-line interference.

## 1. Project Overview

Electrocardiogram (ECG) signals measured through skin electrodes are typically in the millivolt range, making them susceptible to external interference and noise. An analog front-end is therefore required to amplify these signals while minimizing unwanted noise before further processing.

This project implements and simulates an ECG front-end consisting of three main stages:

- Instrumentation amplifier for differential signal amplification.
- Right Leg Drive circuit for common-mode interference reduction.
- Twin-T notch filter for suppressing residual 50 Hz mains noise.

## 2. Circuit Architecture

```text
 RA and LA Electrodes
          |
          v
 +----------------------+
 | Instrumentation Amp  |
 | Gain approximately   |
 | 200                  |
 +----------------------+
          |
          v
 +----------------------+
 | Unity Gain Buffer    |
 +----------------------+
          |
          v
 +----------------------+
 | Twin-T Notch Filter  |
 | Center Frequency     |
 | approximately 50 Hz  |
 +----------------------+
          |
          v
    ECG Output

 RA and LA Signals
          |
          v
 +----------------------+
 | Right Leg Drive      |
 | Feedback Circuit     |
 +----------------------+
          |
          v
     RL Electrode
```

## 3. Instrumentation Amplifier

The first stage uses a conventional three-op-amp instrumentation amplifier built with LT1014 op-amps and powered by ±15 V supplies.

### Component Selection

| Component | Value | Function |
|---|---:|---|
| \(R_g\) | 100 Ω | Controls differential gain |
| \(R_1, R_2\) | 10 kΩ | Feedback resistors |
| \(R_3-R_6\) | 10 kΩ each | Difference amplifier network |

### Gain Calculation

For the first stage of the instrumentation amplifier,

\[
A_v=1+\frac{2R_f}{R_g}
\]

Substituting the selected values,

\[
A_v=1+\frac{2(10,000)}{100}=201
\]

The theoretical gain is approximately 201, or 46 dB.

### Simulation Observations

- A 1 Hz differential input signal was used to represent a simplified ECG waveform.
- A 50 Hz common-mode component was included to examine the effect of power-line interference.
- The simulated output amplitude was approximately 190 mV for a 1 mV differential input, indicating a gain close to the expected value.

The difference between theoretical and simulated gain can arise from circuit loading, op-amp characteristics, and the exact measurement conditions.

## 4. Right Leg Drive Circuit

The Right Leg Drive stage provides active feedback to reduce common-mode voltage at the electrode inputs.

### Component Values

| Component | Value | Purpose |
|---|---:|---|
| \(R_{rld1}, R_{rld2}\) | 10 kΩ | Sense the electrode common-mode voltage |
| \(R_{in}\) | 10 kΩ | Input resistor |
| \(R_{fb}\) | 470 kΩ | Feedback resistor |
| \(R_{body}\) | 51 kΩ | Models the RL electrode/body connection |
| Op-amp | LT1014 | Implements the feedback amplifier |

The nominal inverting amplifier gain is

\[
A_v=-\frac{R_{fb}}{R_{in}}
=-\frac{470k}{10k}=-47
\]

### Working Principle

The RA and LA electrode voltages are combined to estimate their common-mode component. The RLD amplifier inverts and amplifies this signal, feeding it back through the modeled right-leg connection.

This feedback helps counter common-mode interference before it becomes a differential error at the amplifier output. Its actual effectiveness depends on loop stability, electrode impedance, circuit gain, and the modeled body connection.

The RLD circuit complements the notch filter: it targets common-mode interference, whereas the notch filter attenuates residual energy near 50 Hz.

## 5. Twin-T Notch Filter

The final filtering stage uses a passive Twin-T network designed to attenuate 50 Hz interference. A unity-gain buffer is included before the filter to reduce loading from the preceding amplifier stage.

### Component Values

| Component | Value |
|---|---:|
| \(R_9, R_{10}\) | 10 kΩ each |
| \(R_{11}\) | 20 kΩ |
| \(C_1, C_2\) | 320 nF each |
| \(C_3\) | 160 nF |
| Buffer gain | 1 |

### Notch Frequency

For the selected Twin-T component ratios, the nominal notch frequency is

\[
f_0=\frac{1}{2\pi RC}
\]

Using \(R=10\,k\Omega\) and \(C=320\,nF\),

\[
f_0=\frac{1}{2\pi(10,000)(320\times10^{-9})}
\]

\[
f_0\approx49.7\,Hz
\]

This gives a theoretical notch frequency close to 50 Hz.

### Why a Buffer Is Used

The frequency response of a passive Twin-T network can change when it is loaded by the preceding stage. The voltage follower provides a low-output-impedance drive, helping preserve the filter's intended response.

In the simulation, removing the buffer shifted the observed notch frequency to approximately 30 Hz, demonstrating the effect of circuit loading.

### Simulation Results

- The AC analysis showed a notch centered near 50.93 Hz.
- The transient response showed attenuation of the 50 Hz component.
- The lower-frequency ECG waveform remained visible at the output.

## 6. AC Response Analysis

The AC sweep was used to evaluate the circuit's gain and frequency selectivity.

The main observations were:

- Approximately 46 dB of passband gain in the intended signal band.
- A pronounced attenuation region around 50 Hz.
- Recovery of the frequency response on either side of the notch.
- Improved visibility of the low-frequency signal after conditioning.

The measured response depends on the complete circuit configuration, including amplifier gain, loading, and filter tolerances.

## 7. Design Specifications

| Parameter | Specification |
|---|---|
| Instrumentation amplifier | Three-op-amp topology |
| Approximate differential gain | 200 V/V |
| Input signal | 1 mV differential |
| Supply voltage | ±15 V |
| RLD amplifier gain | Approximately −47 V/V |
| Notch frequency | Approximately 50 Hz |
| Body connection model | 51 kΩ |
| Op-amp model | LT1014 |
| Simulation software | LTspice 26.0.1 |

## 8. Project Files

| File | Description |
|---|---|
| `ECG_AFE.asc` | LTspice circuit schematic |
| `README.md` | Project documentation |
| `screenshots/` | Circuit diagrams and simulation plots, if included |

## 9. Key Takeaways

This project provided practical experience with analog biomedical signal conditioning, particularly:

- Designing a three-op-amp instrumentation amplifier and verifying its gain through simulation.
- Understanding how resistor matching influences common-mode rejection.
- Implementing a Right Leg Drive feedback circuit to reduce common-mode interference.
- Studying the influence of source and load impedances on passive filter performance.
- Tuning a Twin-T notch filter for power-line interference near 50 Hz.
- Comparing theoretical calculations with AC and transient simulation results.

The project also illustrates why common-mode feedback and frequency-selective filtering serve complementary purposes in ECG acquisition systems.

## 10. References

1. Horowitz and Hill, *The Art of Electronics* — Analog amplifier design.
2. Texas Instruments, Application Note SLOA034 — ECG front-end design.
3. LT1014 Operational Amplifier Datasheet.
4. Prutchi and Norris, *Design and Development of Medical Electronic Instrumentation* — Biomedical instrumentation and Right Leg Drive circuits.

**Note:** This is a circuit simulation project for educational purposes, not a medically certified ECG device. A practical implementation would require appropriate patient isolation, input protection, current limiting, and electrical safety validation.
