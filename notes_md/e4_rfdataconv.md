# RF Data Converters


---

## 1. RF Data Converters: Analogue to Digital

### Analogue to Digital Converter (ADC) Fundamentals

The core process of Analog-to-Digital conversion consists of measuring a continuous physical signal at fixed time intervals ($f_s$) and mapping those continuous voltage values to a finite set of discrete values via **Quantization**.

* **Resolution Limits:** An $n$-bit ADC discretizes the operating voltage range into $2^n$ distinct levels. For example, Generation 3 RFSoC devices feature 14-bit precision, translating to $2^{14} = 16,384$ digital bins.
* **Quantization Step ($\Delta$):** The minimum voltage difference between adjacent discrete steps is defined by:

$$\Delta = \frac{V_{\max} - V_{\min}}{2^n}$$

The measured analog voltage must be rounded to the closest available quantization step, which introduces structural quantization noise.

### Superconducting Qubit Readout Context

In superconducting quantum computing architectures (e.g., Transmon Qubits), control and readout operations rely heavily on high-frequency microwave signals.

* **Readout Frequencies:** Qubit state readout is typically performed via an on-chip microwave resonator coupled directly to the qubit. These readout signals populate the microwave spectrum, usually ranging from **$5\ \text{GHz}$ to $8\ \text{GHz}$**.
* **Hardware Demands:** Directly processing these signals without bulky, drift-prone analog downconversion mixers requires specialized, ultra-high-speed RF-ADCs capable of running at multi-giga-samples per second (multi-GSps).

---

## 2. Multi-GHz Nyquist Zones and Direct Sampling

According to the Nyquist-Shannon criteria, direct, unambiguous sampling requires the sampling frequency $f_s$ to exceed twice the maximum frequency component of the input signal.

### First Nyquist Zone Direct Capture

The frequency window spanning from $0\ \text{Hz}$ to $\frac{f_s}{2}$ constitutes the **First Nyquist Zone**.

* Because modern RF-ADCs operate at multi-GHz clock rates, their first Nyquist zone naturally spans several gigahertz.
* Any analog signal residing entirely within this first zone can be directly digitized by the ADC core without requiring external analog mixers, IF stages, or local oscillators.

#### Direct Baseband Example

Consider an RF-ADC clock rate of $f_s = 4\ \text{GHz}$. The primary Nyquist zone spans from $0$ to $2\ \text{GHz}$. Any signal in this range is digitized directly. To prevent out-of-band high frequencies from folding down and corrupting the data, an analog anti-aliasing low-pass filter with a sharp cutoff near $2\ \text{GHz}$ must precede the converter.

### Second Nyquist Zone Undersampling

While standard systems use filters to avoid aliasing entirely, RF-ADCs can intentionally exploit aliasing to fold high-frequency signals from upper zones down into the first Nyquist zone. This technique is known as **Undersampling** or **Sub-sampling**.

* **Overcoming Clock Limits:** Generation 3 RF-ADCs feature a maximum native sampling rate of $5\ \text{GSps}$ (implying a first Nyquist zone boundary of $2.5\ \text{GHz}$). However, qubit resonators typically operate at higher frequencies ($5 - 8\ \text{GHz}$).
* **Direct RF Sampling:** By using a high-quality analog **bandpass filter** instead of a low-pass filter, the first Nyquist zone is cleared of baseband signals. This allows a signal from the Second Nyquist Zone ($\frac{f_s}{2}$ to $f_s$) to fold cleanly into the first zone without overlap or interference.

> **Spectral Inversion Warning:** When a signal from an even Nyquist zone (like Zone 2 or Zone 4) folds down into the first zone, its spectral orientation is inverted (flipped left-to-right). This spectral inversion must be mathematically corrected downstream in the digital domain.

### Practical Frequency Boundaries

While higher odd Nyquist zones (Zone 3, Zone 5, etc.) fold into the first zone with their original spectral orientation, the physical input stages of an RF-ADC impose practical frequency limits.

The internal analog input buffers, sample-and-hold circuits, and packaging track-lines attenuate signals at higher frequencies. The certified analog input bandwidth boundaries across AMD Xilinx RFSoC generations are:

* **Generation 1:** Characterized up to $4\ \text{GHz}$
* **Generation 2:** Characterized up to $5\ \text{GHz}$
* **Generation 3:** Characterized up to $6\ \text{GHz}$

*Note: Custom boards like the RFSoC 4x2 utilize specialized input baluns capable of handling an extended bandwidth up to $10\ \text{GHz}$.*

---

## 3. Analogue Front-End Filtering Strategies

Choosing an analog front-end filter design requires balancing noise performance against system flexibility:

$$\begin{array}{lll}
\hline
\textbf{Filter Architecture} & \textbf{Fixed Application Approach} & \textbf{Qubit Characterization Approach} \\ \hline
\textbf{Filter Classification} & \text{Narrowband RF Bandpass Filter} & \text{Full-Zone Low-Pass / Bandpass Filter} \\
\textbf{Passband Width} & \text{Matched tightly to the signal bandwidth} & \text{Spans the entire target Nyquist Zone} \\
\textbf{Noise Performance} & \text{Excellent out-of-band rejection and SNR} & \text{Higher broadband noise floor integration} \\
\textbf{System Flexibility} & \text{Rigid; cannot track frequency shifts} & \text{Highly adaptive for multi-qubit tuning} \\ \hline
\end{array}$$

* **Zone 1 Configuration:** Uses a low-pass filter with a sharp cutoff near $\frac{f_s}{2}$ to block all higher-order image components from folding down.
* **Zone 2 Configuration:** Uses a dedicated bandpass filter spanning $\frac{f_s}{2}$ to $f_s$. This filter clears out baseband noise from Zone 1 and blocks higher-order content from Zone 3 and above, ensuring that only the target Zone 2 signal folds cleanly into the digitized spectrum.

---

## 4. Hardware Architecture: Interleaved Sub-ADC Tiles

To achieve multi-giga-sample capture rates without requiring ultra-fast, high-power single-core converters, RFSoC devices employ a hardware strategy called **Time-Interleaved ADCs**.

```
                           +--------------+
                      ---> |  Sub-ADC 1   | ---+
                      |    | (Phase = 0)  |    |
                      |    +--------------+    |
                      |    +--------------+    |
  Analog Input Signal |---> |  Sub-ADC 2   | ---|---> Combined High-Rate Digital Stream
                      |    | (Phase = dt) |    |      (Effective Rate = m * f_sub)
                      |    +--------------+    |
                      |           ...          |    |
                      |    +--------------+    |
                      ---> |  Sub-ADC m   | ---+
                           +--------------+

```

### The Interleaving Principle

A high-speed RF-ADC channel is built from an array of $m$ slow internal sub-ADC cores running in parallel. They share a single analog input line but are clocked at uniformly staggered time intervals.

By shifting the clock phase of each successive sub-ADC core, the effective sampling rate of the combined channel increases by a factor of $M$ (the interleaving factor) relative to the clock rate of an individual sub-ADC core:

$$f_{s,\text{effective}} = m \times f_{\text{sub-clock}}$$

To ensure accurate sampling, the precise phase relationship for the $n^{\text{th}}$ sub-ADC core must satisfy:

$$\theta_n = \frac{2\pi(n - 1)}{m}, \quad \text{where } n = 1, 2, \dots, m$$

### Tile Structures: Dual vs. Quad Layouts

The RFDC IP block organizes these converters into groups called **Tiles**. The maximum sampling speed of a tile depends directly on its sub-ADC interleaving factor:

* **Dual-Channel Tiles:** Each RF-ADC channel integrates **8 interleaved sub-ADCs**.
* **Quad-Channel Tiles:** Each RF-ADC channel integrates **4 interleaved sub-ADCs**.

Because Dual-Channel Tiles use twice as many interleaved sub-ADC cores per channel, they can run at twice the maximum sampling speed of Quad-Channel Tiles within the same hardware generation:

| RFSoC Generation & Device | Channel Type | Total Internal Channels | Maximum Sampling Rate |
| --- | --- | --- | --- |
| **Gen 1: ZU28DR** | $4\times$ Dual Tiles | 8 ADCs | $4.096\ \text{GSps}$ |
| **Gen 2: ZU39DR** | $4\times$ Quad Tiles | 16 ADCs | $2.220\ \text{GSps}$ |
| **Gen 3: ZU43DR** | $4\times$ Single Tiles | 4 ADCs | $5.0\ \text{GSps}$ |
| **Gen 3: ZU46DR** | Quad / Dual Mixed | Variable | Quad: $2.5\ \text{GSps}$ / Dual: $5.0\ \text{GSps}$ |
| **Gen 3: ZU48DR** | $4\times$ Dual Tiles | 8 ADCs | $5.0\ \text{GSps}$ |
| **Gen 3: ZU49DR** | $4\times$ Quad Tiles | 16 ADCs | $2.5\ \text{GSps}$ |

---

## 5. The Digital Downconverter (DDC) Pipeline

Once the analog signal is digitized by the interleaved sub-ADC array, it enters a dedicated hardware processing pipeline. This pipeline shapes, downconverts, and filters the high-speed data stream before passing it to the Programmable Logic (PL) fabric.

```
  +---------+     +---------+     +---------+     +---------+     +-------------+     +----------+
  | Analog  | --> |  Core   | --> | Threshold | --> |   QMC   | --> |   Digital   | --> |    I/Q   | --> PL
  | Buffers |     | RF-ADC  |     | Detector|     |  Block  |     |Complex Mixer|     |Decimators|     Fabric
  +---------+     +---------+     +---------+     +---------+     +-------------+     +----------+
                       ^                                                 ^                     ^
                       |                                                 |                     |
                 [Gen 3: DSA]                                      [NCO Tuning]          [1x to 40x]

```

### Generation 3 Frontend Additions: The DSA

In Generation 3 devices, a programmable **Digital Step Attenuator (DSA)** is integrated directly ahead of the analog input buffers.

The DSA dynamically scales the amplitude of incoming RF signals to utilize the ADC's full dynamic range. This optimization helps prevent harmonic distortion, signal clipping, and unexpected over-voltage conditions.

### Quadrature Modulation Correction (QMC)

When processing complex physical I/Q channels, minor imbalances in gain or phase between the twin paths can degrade signal integrity. The QMC block provides real-time digital calibration to correct for these phase and gain mismatches, ensuring high orthogonal balance.

### Digital Complex Mixer and NCO Tuning

The digital complex mixer shifts the high-frequency input signal down to baseband by multiplying it with coherent sine and cosine waveforms. These local reference signals are generated by a highly tunable **48-bit Numerically Controlled Oscillator (NCO)** operating on heterodyne principles.

The mixer can be configured to run in three operational modes:

1. **Bypass Mode:** Disables the internal mixing logic completely. The raw, real-valued digital samples pass through unchanged.
2. **Coarse Mode:** Limits frequency translation to a fixed set of fractional carrier options ($\frac{f_s}{2}$, $\frac{f_s}{4}$, or $-\frac{f_s}{4}$). This hardware configuration bypasses the NCO, which saves power and reduces latency.
3. **Fine Mode:** Enables the full 48-bit NCO, allowing the mixer to translate any arbitrary center frequency between $-\frac{f_s}{2}$ and $+\frac{f_s}{2}$ down to baseband.

#### Correcting Inverted Spectra

When sampling a signal from an even Nyquist zone, its folded spectrum is inverted within the first zone. To correct for this, engineers can configure the NCO with a **negative tuning frequency**. This mathematical adjustment automatically flips the spectrum back to its correct orientation during downconversion.

### Programmable Decimation Engine

After downconversion, the signal is routed through a programmable decimation chain. This stage reduces the data sampling rate to match the signal's actual baseband bandwidth, lowering the processing burden on the FPGA logic.

The decimation chain is built from a cascade of digital half-band low-pass filters. Each active filter stage reduces the sample rate by half while attenuating any out-of-band noise or signals above the new Nyquist limit.

* **Gen 1 & Gen 2 Performance:** Supports integer decimation factors up to **$8\times$** ($1\times, 2\times, 4\times, 8\times$).
* **Gen 3 Performance:** Features an expanded filter cascade that supports decimation factors up to **$40\times$** ($1\times, 2\times, 4\times, 6\times, 8\times, 10\times, 12\times, 20\times, 24\times, 40\times$).

---

## 6. RF Data Converters: Digital to Analogue

The role of an RF-DAC is to convert discrete-time digital samples from the programmable logic back into a continuous-time physical analog voltage wave.

### Zero-Order Hold (ZOH) Distortions

To construct a continuous waveform from digital points, standard DACs use a **Zero-Order Hold (ZOH)** technique. The DAC latches and maintains the voltage level of each sample constant for the entire duration of the clock period ($t_s = \frac{1}{f_s}$).

This constant latching creates a sharp, step-like output waveform. In the frequency domain, this response can be modeled as a rectangular pulse, which applies a characteristic **Sinc ($\frac{\sin x}{x}$)** envelope to the output spectrum:

$$H_{\text{zoh}}(f) = \frac{\sin\left(\frac{\pi f}{f_s}\right)}{\frac{\pi f}{f_s}}$$

[Image comparing Normal ZOH mode roll-off with Mix-Mode frequency shaping]

The ZOH technique introduces two primary spectral challenges:

1. **High-Frequency Image Generation:** The sharp transitions of the step-like waveform generate unwanted image frequencies in upper Nyquist zones, centered at $n \cdot f_s \pm f_{\text{fundamental}}$.
2. **In-Band Aperture Roll-Off:** The sinc envelope causes a non-linear loss of amplitude across the first Nyquist zone. This roll-off results in a **$\approx 4\ \text{dB}$ drop in gain** at the zone boundary ($\frac{f_s}{2}$), which represents a loss of more than 50% of the signal's output power.

### Counteracting Amplitude Roll-Off

To counteract the amplitude drop caused by the sinc envelope, systems use two main approaches:

* **Oversampling:** Increasing the DAC's sampling rate relative to the target signal frequency keeps the signal close to DC, minimizing the effects of the sinc curve roll-off.
* **Inverse Sinc Digital Pre-Emphasis:** The digital data can be passed through an **Inverse Sinc Filter** prior to the DAC core. This filter boosts the gain of higher-frequency components to compensate for the ZOH roll-off, delivering a flat amplitude response across roughly 90% of the first Nyquist zone.

---

## 7. Dual-Zone DAC Modes: Normal vs. Mix-Mode

Just as an RF-ADC can sample high-frequency inputs via aliasing, an RF-DAC can exploit its high-frequency image components to synthesize output signals directly into upper Nyquist zones.

```
  Normal ZOH Mode (Pulse Shape)             Mix-Mode (Pulse Shape)
  +---------------+                         +-------+
  |               |                         |       |
  |               |                         |       |
--+               +--                     --+       +       +--
                                                    |       |
                                                    |       |
                                                    +-------+
  [Optimized for Nyquist Zone 1]             [Optimized for Nyquist Zone 2]

```

### 1. Normal ZOH Mode

In Normal Mode, the DAC holds each sample constant for the full duration of the clock cycle ($t_s$). This configuration concentrates the output energy within the **First Nyquist Zone**, making it ideal for baseband signal synthesis.

### 2. Mix-Mode

To optimize performance in the **Second Nyquist Zone** ($\frac{f_s}{2}$ to $f_s$), the DAC can be switched to Mix-Mode. In this configuration, the output pulse shape is inverted halfway through each clock period ($t_s/2$).

In the frequency domain, this phase inversion modifies the DAC's transfer function to follow a shifted sinc profile:

$$H_{\text{mix}}(f) = \frac{\sin^2\left(\frac{\pi f}{2 f_s}\right)}{\frac{\pi f}{2 f_s}}$$

This reshaping shifts the peak output energy out of the first zone and into the second Nyquist zone, providing a higher and flatter gain profile across that upper frequency band.

#### High-Frequency Synthesis Example

Suppose a system needs to generate an $8\ \text{GHz}$ pure sinusoidal wave using an RF-DAC running at $f_s = 10\ \text{GSps}$. Direct synthesis within the first Nyquist zone is limited to $5\ \text{GHz}$.

However, synthesizing a $2\ \text{GHz}$ tone inside the digital engine naturally creates a high-frequency image component at $f_s - f = 10\ \text{GHz} - 2\ \text{GHz} = 8\ \text{GHz}$ in the second zone. By operating the DAC tile in **Mix-Mode** and adding an external analog bandpass filter centered at $8\ \text{GHz}$, the $2\ \text{GHz}$ primary tone is suppressed, and the $8\ \text{GHz}$ image is isolated as a clean, high-frequency output.

---

## 8. The Digital Upconverter (DUC) Pipeline

The Generation 3 RF-DAC tiles feature an integrated, multi-stage Digital Upconverter (DUC) pipeline that prepares digital signals from the FPGA fabric for high-speed analog conversion.

```
  +--------+     +---------------+     +---------------+     +-------------+     +-----------+     +---------+
  | Gearbox| --> | Programmable  | --> | Quadrature    | --> |   Digital   | --> |   Image   | --> | 14-Bit  | --> RF
  |  FIFO  |     | Interpolator  |     | CorrectionQMC |     |Complex Mixer|     | Rejection |     |DAC Core |     Out
  +--------+     +---------------+     +---------------+     +-------------+     +-----------+     +---------+
                        ^                                           ^                  ^
                        |                                           |                  |
                  [1x to 40x Stage]                            [48-Bit NCO]       [Gen 3: IMR]

```

### Programmable Interpolation Engine

To match low-rate baseband signals from the FPGA to the high clock rates of the RF-DAC core, the pipeline uses a programmable interpolation engine. This engine can be configured to process either real-valued inputs or complex I/Q data pairs.

The interpolation process is handled by a cascade of four independent upsamplers and low-pass Finite Impulse Response (FIR) filters:

* **Stage 1 Selection:** Features three multiplexed, sharp-cutoff filters (**FIR1a, FIR1b, FIR1c**) providing native $2\times$, $3\times$, or $5\times$ interpolation rates. Only one filter in this initial block can be active at a time.
* **Downstream Stages:** Cascades through **FIR2, FIR3, and FIR4** to build higher overall interpolation factors.
* **Supported Interpolation Rates:** Gen 3 devices support a wide range of overall interpolation factors: $1\times, 2\times, 3\times, 4\times, 5\times, 6\times, 8\times, 10\times, 12\times, 16\times, 20\times, 24\times, \text{and } 40\times$.

### Digital Complex Modulation Mixer

The digital complex mixer modulates baseband I/Q data up to a target intermediate frequency before it reaches the DAC core.

* Functions identically to the ADC mixer but operates in reverse to modulate signals rather than demodulate them.
* Integrates a **48-bit NCO** fine mixer alongside a low-power coarse mixer ($\frac{f_s}{2}, \pm\frac{f_s}{4}$).
* Supports multi-tile synchronization, allowing the NCO phase wheels across multiple independent tiles to align perfectly. This capability is critical for maintaining phase coherence across large multi-qubit control systems.

### Generation 3 Image Rejection (IMR) Filter

Generation 3 architectures add an **Image Rejection (IMR) Filter** downstream from the core mixing and modulation stages.

* **Additional Interpolation:** Enabling the IMR filter introduces a final $2\times$ upsampling step, which can extend the system's maximum total interpolation factor from $40\times$ up to **$80\times$**.
* **Selectable Filter Profiles:** The IMR can be configured as a low-pass filter to retain the signal within the first Nyquist zone while suppressing the second-zone image, or as a high-pass filter to do the opposite.
* **Stopband Attenuation:** Both the low-pass and high-pass IMR profiles feature symmetric designs that deliver $60\text{ dB}$ of out-of-band stopband attenuation.

---

## 9. Comprehensive Architectural Layout Reference

$$\begin{array}{lll}
\hline
\textbf{Functional Capability} & \textbf{RF-ADC Processing Subsystem} & \textbf{RF-DAC Processing Subsystem} \\ \hline
\textbf{Native Resolution} & 14\text{-bits} & 14\text{-bits} \\
\textbf{Primary Operation} & \text{Downconversion / Decimation} & \text{Upconversion / Interpolation} \\
\textbf{Frontend Conditioning} & \text{Digital Step Attenuator (DSA) [Gen 3]} & \text{Variable Output Power (VOP) [Gen 3]} \\
\textbf{In-Band Error Calibration} & \text{Quadrature Modulation Correction (QMC)} & \text{Quadrature Modulation Correction (QMC)} \\
\textbf{Frequency Shifting Engine} & \text{48-bit NCO / Coarse Heterodyne Mixer} & \text{48-bit NCO / Coarse Heterodyne Mixer} \\
\textbf{Rate-Change Factor Ranges} & 1\times \text{ to } 40\times \text{ Decimation [Gen 3]} & 1\times \text{ to } 80\times \text{ Interpolation (with IMR)} \\
\textbf{Target Nyquist Zones} & \text{Zone 1 (Direct) or Zone 2 (Aliased)} & \text{Zone 1 (Normal Mode) or Zone 2 (Mix-Mode)} \\
\textbf{High-Frequency Filter Step} & \text{Analog Front-End Anti-Alias Filter} & \text{Digital Image Rejection (IMR) + Analog Filter} \\ \hline
\end{array}$$