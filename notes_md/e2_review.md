# Review of Digital Signal Processing


---

## 1. Analog-to-Digital Conversion (ADC)

Analog-to-Digital Conversion is the foundational process of translating a continuous physical waveform $z(t)$ into a discrete-time, discrete-amplitude digital equivalent suitable for computational processing. This conversion is achieved through two consecutive operations: **Sampling** and **Quantization**.

```
    +------------------+     Sampling     +-------------------+     Quantization     +--------------------+
--->| Analog Wave z(t) | -------------->  | Discrete in Time  | -------------------> | Discrete in Time   | --->
    | (Continuous)     |   [t = n*ts]     | (Continuous Amp)  |     [Map to q]       | & Amplitude (bits) |
    +------------------+                  +-------------------+                      +--------------------+

```

---

## 2. Sampling Dynamics

Sampling discretizes a continuous-time signal by measuring its instantaneous amplitude at uniform time intervals.

* **Sampling Period ($t_s$):** The constant time interval between consecutive measurements.
* **Sampling Rate ($f_s$):** The frequency at which the analog signal is measured, defined as the inverse of the sampling period:

$$f_s = \frac{1}{t_s}$$

The choice of $f_s$ establishes the limits of the system's capture bandwidth and determines how closely the digital representation tracks the original physical signal.

### The Effect of Sampling Rate Selection

Consider a baseline $100\ \text{Hz}$ sinusoidal wave sampled at three different frequencies:

#### 1. High Sampling Rate ($f_s = 2\ \text{kHz}$)

Sampling at $2\ \text{kHz}$ yields $20$ samples per period ($\frac{2000\ \text{Hz}}{100\ \text{Hz}}$). The digitized points provide an accurate representation of the signal's true time-domain profile.

#### 2. Moderate Sampling Rate ($f_s = 500\ \text{Hz}$)

Sampling at $500\ \text{Hz}$ yields $5$ samples per period. This provides a balanced trade-off, preserving the essential signal characteristics while reducing the storage and processing burden on the digital hardware.

#### 3. Low Sampling Rate ($f_s = 80Hz$)

Sampling below the signal frequency (providing fewer than $2$ samples per period) alters the perceived waveform. The sampled data points track a false lower frequency. This distortion is known as **Aliasing**.

In this under-sampled scenario, the apparent frequency of the aliased signal ($f_{\text{alias}}$) matches the difference between the true signal frequency and the sampling rate:

$$f_{\text{alias}} = |f_{\text{sine}} - f_s| = |100\ \text{Hz} - 80\ \text{Hz}| = 20\ \text{Hz}$$

---

## 3. Signal Characterization and Frequency Classification

Before applying sampling limits, signals are categorized by their spectral distribution:

* **Baseband Signals:** Signals whose spectral energy begins at or near $0\ \text{Hz}$ and extends to a given cutoff frequency (e.g., raw audio or unmodulated data streams).
* **Bandpass Signals:** Signals whose spectral content is entirely contained within a specific frequency band that does not border $0\ \text{Hz}$. This profile is typical of high-frequency modulated communication carriers or raw qubit readout responses.
* **Bandlimited Signals:** A general classification for any signal whose total spectral energy is restricted to a defined range between an upper boundary ($f_h$) and a lower boundary ($f_l$).

---

## 4. The Nyquist-Shannon Sampling Theorem

To ensure a continuous bandlimited signal can be perfectly reconstructed from its discrete samples without information loss, the sampling rate must exceed twice the maximum frequency or bandwidth of the signal.

### Baseband Formulation

For standard baseband signals extending up to a maximum frequency $f_{\max}$, the minimum sampling criteria (the **Nyquist Rate**) is defined by:

$$f_s > 2f_{\max}$$

If $f_s$ falls below this threshold, all spectral components exceeding $\frac{f_s}{2}$ cross into adjacent bands, causing irreversible aliasing distortion.

### Bandpass Formulation

For a bandpass signal restricted between a low frequency $f_l$ and a high frequency $f_h$, the criteria adapts to the absolute bandwidth of the signal ($\Delta f = f_h - f_l$):

$$f_s > 2(f_h - f_l)$$

This relationship allows the system to use a much lower sampling rate than direct baseband sampling would require. It relies on the technique of **Undersampling**, placing the signal within specific frequency zones called **Nyquist Zones**.

---

## 5. Nyquist Zones and Aliasing Mechanics

The sampling rate $f_s$ divides the frequency spectrum into equal structural segments or intervals of width $\frac{f_s}{2}$. These intervals are called Nyquist Zones.

* **First Nyquist Zone:** Ranges from $0$ to $\frac{f_s}{2}$. This is the primary window for direct sampling without aliasing.
* **Second Nyquist Zone:** Ranges from $\frac{f_s}{2}$ to $f_s$. Any signal component in this zone is folded or mirrored into the first zone upon sampling.
* **Third Nyquist Zone:** Ranges from $f_s$ to $\frac{3f_s}{2}$. Signals fold forward into the first zone, maintaining their original spectral orientation.

### General Spectral Mapping Rule

The relationship mapping any absolute physical frequency $f$ to its alias location $f_{\text{alias}}$ within the primary first zone ($0 \le f_{\text{alias}} \le \frac{f_s}{2}$) is governed by:

$$f_{\text{alias}} = \left| f - n f_s \right|$$

Where $n$ is an integer chosen such that the resulting frequency falls within the bounds of the first Nyquist zone.

---

## 6. Undersampling Applications in Quantum Control

Superconducting qubits operate at microwave frequencies in the gigahertz range ($4 - 8\ \text{GHz}$). Digitizing these signals using direct baseband sampling would require ultra-high-speed converters operating above $16\ \text{GSps}$, which adds substantial power, computational, and financial overhead.

### Strategic Implementation

Undersampling addresses this challenge by intentionally operating the ADC at a lower sampling rate than the absolute carrier frequency, provided the total signal bandwidth remains narrower than $\frac{f_s}{2}$ and fits entirely within a single Nyquist zone.

```
       Microwave Qubit Signal (e.g., 1.65 GHz) --> [Bandpass Filter: 1.6-1.7 GHz]
                                                            |
                                                            v
                                                  [ADC Sampler at fs = 1 GHz]
                                                            |
                                                            v
                                         Digital Output Signal (0.3 - 0.4 GHz)

```

### Downconversion Example

Consider a modulated qubit readout response spanning $1.6\ \text{GHz}$ to $1.7\ \text{GHz}$ ($\text{Bandwidth} = 100\ \text{MHz}$), sampled using an ADC running at $f_s = 1\ \text{GHz}$. The Nyquist zones are $500\ \text{MHz}$ wide, placing the signal entirely inside the 4th Nyquist zone ($1.5\ \text{GHz}$ to $2.0\ \text{GHz}$).

Applying the mapping rule:

* The $1.6\ \text{GHz}$ edge maps to: $|1.6\ \text{GHz} - 2(1\ \text{GHz})| = 400\ \text{MHz}$
* The $1.7\ \text{GHz}$ edge maps to: $|1.7\ \text{GHz} - 2(1\ \text{GHz})| = 300\ \text{MHz}$

The undersampling process shifts the high-frequency microwave band down to an intermediate baseband window ($300 - 400\ \text{MHz}$) inside the first zone. This approach achieves direct digital downconversion, eliminating the need for an analog mixing stage.

> **Critical Constraint:** The original signal spectrum must fit entirely within a single Nyquist zone. If a signal crosses a zone boundary, its components fold back on top of each other from opposite directions, corrupting the digitized data.

---

## 7. Sampling Jitter

In a real-world ADC, the sampling clock experiences tiny timing variations rather than maintaining a perfectly uniform period $t_s$. This variation is known as **Sampling Jitter**.

Jitter introduces amplitude noise when sampling a time-varying signal. If a sample is taken slightly early or late by an offset $\delta t$, the measured voltage deviates from its true value by an error $\delta V$. This amplitude error is directly proportional to the signal's rate of change (slew rate):

$$\delta V \approx \frac{dz(t)}{dt} \delta t$$

Because high-frequency signals change rapidly over time, even minor timing jitter can cause significant amplitude errors. To maintain clean phase coherence and minimize this noise floor in high-frequency applications like qubit control, systems require ultra-stable, low-phase-noise clock sources.

---

## 8. Quantization Principles

Quantization transforms a continuous analog voltage sample into a discrete digital value by mapping it to the nearest available amplitude step.

### Bit Resolution and Step Size

An $N$-bit ADC provides $2^N$ discrete vertical quantization levels distributed across its full-scale input voltage range ($V_{\text{FS}}$). For a peak operating boundary from $-V_{\max}$ to $+V_{\max}$, the total full-scale range is $V_{\text{FS}} = 2V_{\max}$. The minimum voltage increment or step size ($q$) is defined as:

$$q = \frac{V_{\text{FS}}}{2^{N-1}}$$

[Image comparing quantization step resolution between a 4-bit and a 6-bit converter]

Increasing the bit depth $N$ provides a finer grid of quantization steps, allowing the digital system to approximate the continuous analog signal more accurately.

### The Ideal Quantization Error Model

The difference between the continuous analog voltage and its assigned discrete step is the quantization error. The maximum error occurs when a sample falls exactly halfway between two steps, limiting the absolute error to $\pm \frac{q}{2}$.

For a dynamic, complex input signal, this error is typically modeled as a uniform random variable with a flat Probability Density Function (PDF) across the step interval:

$$p(e) = \begin{cases} \frac{1}{q}, & -\frac{q}{2} \le e \le \frac{q}{2} \\ 0, & \text{otherwise} \end{cases}$$

Integrating the squared error across this distribution yields the theoretical **Quantization Noise Power ($n_{\text{adc}}$)**:

$$n_{\text{adc}} = \int_{-\frac{q}{2}}^{\frac{q}{2}} e^2 \cdot p(e) \, de = \int_{-\frac{q}{2}}^{\frac{q}{2}} \frac{e^2}{q} \, de = \frac{q^2}{12}$$

This noise floor is distributed evenly across the primary baseband from $0$ to $\frac{f_s}{2}$. Increasing the converter's bit depth reduces the step size $q$, which lowers the quantization noise floor and prevents weak signals from being masked.

---

## 9. Dynamic Range and Spectral Purity

### Dynamic Range (DR)

The Dynamic Range defines the ratio between the largest reliable signal an ADC can measure without clipping and its smallest detectable increment. Expressed in decibels (dB) for an $N$-bit system, it follows the linear relationship:

$$\text{DR} = 20 \log_{10}\left(2^N\right) = 20 N \log_{10}(2) \approx 6.02 N\ \text{[dB]}$$

* A **10-bit converter** provides a dynamic range of $\approx 60.2\ \text{dB}$.
* A **14-bit converter** improves this range to $\approx 84.3\ \text{dB}$.

### Frequency Spurs and SFDR

If the input waveform is perfectly periodic (such as a pure sine wave), the quantization errors repeat in a predictable pattern rather than acting as random noise. This periodicity generates discrete, unwanted harmonic tones called **Spurs** in the frequency domain.

These spurs are evaluated using the **Spurious-Free Dynamic Range (SFDR)** metric, which measures the ratio in dB between the fundamental signal power and the highest unwanted spur component.

---

## 10. Digital Hardware Data Representation

To minimize logic gate counts, power consumption, and processing latency, high-speed digital hardware elements (like FPGAs and ASICs) typically process fractional values using **Fixed-Point Arithmetic** rather than floating-point engines.

### The Q-Format Configuration

Fixed-point numbers use a structural format designated as **$\text{Q}n.b$**:

* **$n$:** The number of bits assigned to represent the integer component.
* **$b$:** The number of bits assigned to represent the fractional component.
* **Total Word Length:** Defined by the sum $n + b$.

Bits are ordered from the Most Significant Bit (MSB) on the left to the Least Significant Bit (LSB) on the right, with each position scaled by a binary weight based on its distance from the radix point:

$$\text{Weight} = 2^{\text{index}}$$

### Structural Formats

1. **Unsigned Fixed-Point:** All bit positions have a positive numerical scale factor. The maximum representable value is $2^n - 2^{-b}$.
2. **Two's Complement Fixed-Point:** The MSB acts as a sign indicator with a negative weight (a value of `1` indicates a negative number). For a $\text{Q}n.b$ two's complement layout, the operating bounds are:

$$\text{Minimum Value} = -2^{n-1}, \quad \text{Maximum Value} = 2^{n-1} - 2^{-b}$$

---

## 11. Fixed-Point Arithmetic Math Operations

### Addition and Subtraction

When adding or subtracting two fixed-point numbers with aligned binary points, the operations follow standard integer arithmetic logic. However, combining two values can produce an output that exceeds the available bit range. To prevent overflow, the word length must expand by one bit ($n + 1$).

#### Example Case

Consider adding two unsigned $8\text{-bit}$ numbers configured in a $\text{Q}5.3$ layout ($5$ integer bits, $3$ fractional bits).

* **Maximum possible value:** $2^5 - 2^{-3} = 31.875$
* **Worst-case sum:** $31.875 + 31.875 = 63.75$

To safely store this maximum sum without clipping, the hardware requires a $9\text{-bit}$ output word configured as $\text{Q}6.3$:

$$\begin{array}{rrc}
11111.111_2 & (31.875) \\
+\quad 11111.111_2 & (31.875) \\
\hline
111111.110_2 & (63.750)
\end{array}$$

### Multiplication

Multiplying two fixed-point numbers increases both the integer and fractional bit widths. When a value with a $\text{Q}n_1.b_1$ layout is multiplied by a value with a $\text{Q}n_2.b_2$ layout, the raw product format expands to:

$$\text{Output Format} = \text{Q}(n_1 + n_2).(b_1 + b_2)$$

#### Example Case

Multiplying two independent $8\text{-bit}$ unsigned $\text{Q}5.3$ values yields a product with a total width of $16\text{-bits}$, split into $10$ integer bits and $6$ fractional bits ($\text{Q}10.6$). In practical system designs, this raw product is often truncated or rounded downstream to match the input bit width of subsequent processing stages.

---

## 12. Digital-to-Analog Conversion (DAC) Mechanics

A Digital-to-Analog Converter performs the inverse operation of an ADC, converting a sequence of discrete digital codes into a continuous analog voltage waveform.

### The Zero-Order Hold (ZOH) Effect

After converting a digital value to an analog level, a standard DAC maintains that voltage constant for an entire clock cycle until the next sample update arrives. This step-like output behavior is known as a **Zero-Order Hold (ZOH)** function.

In the time domain, the ZOH response acts as a rectangular pulse of duration $t_s$. In the frequency domain, this rectangular response applies a **Sinc ($\frac{\sin x}{x}$)** envelope to the output spectrum, scaling the amplitude of the synthesized signal:

$$H_{\text{zoh}}(f) = e^{-j\pi f t_s} \left( \frac{\sin\left(\pi f t_s\right)}{\pi f t_s} \right)$$

[Image showing a DAC output spectrum shaped by the characteristic sinc roll-off envelope]

This sinc envelope introduces two primary spectral effects:

1. **Aperture Distortion:** It causes a non-linear amplitude roll-off that dampens higher-frequency signals within the first Nyquist zone, even if they are below the Nyquist limit ($\frac{f_s}{2}$).
2. **Image Frequencies:** The sharp edges of the step-like time-domain waveform generate unwanted image frequencies that repeat across higher Nyquist zones. These images are centered around integer multiples of the sampling frequency ($f_s \pm f_{\text{fundamental}}$).

---

## 13. Reconstruction Filters and Oversampling Strategies

### Reconstruction Filtering

To smooth out the step-like ZOH waveform and eliminate high-frequency image components from the upper Nyquist zones, an analog **Reconstruction Filter** (a low-pass or band-pass filter) is placed at the DAC output.

If a synthesized signal occupies the entire first Nyquist zone up to $\frac{f_s}{2}$, the image frequency appears immediately adjacent to it. Separating the two requires an expensive analog filter with an extremely sharp cutoff. Because of this practical limitation, the maximum usable output frequency for standard DAC configurations is typically restricted well below the theoretical Nyquist limit.

```
+---------+  Unfiltered Output  +-----------------------+  Filtered Output  +--------------+
| DAC Core| ------------------> | RECONSTRUCTION FILTER | ----------------> | Smooth Clean |
| Output  | (Step-like + Images)| [Low-Pass / Band-Pass]|   (Images Removed)| Waveform     |
+---------+                     +-----------------------+                   +--------------+

```

### Mitigation Strategies

To simplify the analog filter design and minimize aperture distortion, systems often employ two main techniques:

* **Oversampling:** Running the DAC core at a higher sampling rate increases the width of the Nyquist zones. This pushes the unwanted image frequencies further away from the target baseband signal, allowing the use of a simpler reconstruction filter with a gradual cutoff.
* **Inverse Sinc Digital Filtering:** A digital pre-distortion filter can be implemented in the FPGA fabric ahead of the DAC. This filter applies an inverse sinc gain profile ($H(f) \propto \frac{\dots}{\sin x}$) that counteracts the ZOH roll-off, flattening the overall frequency response across the first Nyquist zone.

---

## 14. Essential DSP Formula Reference Sheet

$$\begin{array}{ll}
\hline
\textbf{DSP System Parameter} & \textbf{Mathematical Formulation} \\ \hline
\text{Sampling Period to Rate Mapping} & f_s = \frac{1}{t_s} \\
\text{Baseband Nyquist Sampling Criteria} & f_s > 2 f_{\max} \\
\text{Bandpass Nyquist Sampling Criteria} & f_s > 2 (f_h - f_l) \\
\text{Nyquist Zone Aliasing Map Location} & f_{\text{alias}} = |f - n f_s| \quad (0 \le f_{\text{alias}} \le \frac{f_s}{2}) \\
\text{Discrete Linear Quantization Step Size} & q = \frac{V_{\text{FS}}}{2^N} \\
\text{Theoretical Quantization Noise Power} & n_{\text{adc}} = \frac{q^2}{12} \\
\text{Converter Decibel Dynamic Range} & \text{DR} = 20 \log_{10}(2^N) \approx 6.02 N\ \text{[dB]} \\
\text{Two's Complement Q-Format Bounds} & \text{Range} = \left[ -2^{n-1} \ \ \dots \ \ 2^{n-1} - 2^{-b} \right] \\
\text{Addition Expansion Word Length} & \text{Width}_{\text{sum}} = n + 1 \\
\text{Multiplication Product Word Length} & \text{Format}_{\text{prod}} = \text{Q}(n_1 + n_2).(b_1 + b_2) \\
\text{DAC Zero-Order Hold Frequency Envelope} & H_{\text{zoh}}(f) = \frac{\sin(\pi f t_s)}{\pi f t_s} \\ \hline
\end{array}$$