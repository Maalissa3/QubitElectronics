# Multirate Operations


---

## 1. Limitations of Conventional ADC Systems

In a standard Analog-to-Digital Converter (ADC) operating within the first Nyquist Zone ($0 \le f \le \frac{f_s}{2}$), the input stage must be preceded by an analog anti-aliasing low-pass filter.

### The Anti-Aliasing Constraint

* **Spectral Preservation:** The filter isolates the target frequency components located within the first Nyquist Zone while attenuating all out-of-band high-frequency components.
* **The Aliasing Threat:** Without this analog stage, any high-frequency noise or signal component above $\frac{f_s}{2}$ folds back directly into the primary band, corrupting the target signal.
* **The "Brick-Wall" Ideal:** Perfect spectral isolation requires a non-causal "brick-wall" filter with an instantaneous transition band. This response cannot be physically constructed in the analog domain, nor can it be realized computationally as a digital filter.

### Real-World Filtering Compromises

Because physical analog components have finite roll-off slopes, hardware engineers must accept a design trade-off:

1. **In-Band Attenuation:** The filter's cutoff can be placed below the upper limit of the first Nyquist zone, which unintentionally dampens high-frequency components of interest.
2. **Aliasing Overlap:** The transition band can be allowed to extend past $\frac{f_s}{2}$ into the second Nyquist zone, which introduces a controlled level of aliasing into the digitized output.

As a filter's specifications become tighter (requiring narrower transition bands and higher stopband attenuation), the physical circuit becomes significantly more complex, sensitive to component tolerances, and expensive to manufacture.

---

## 2. The Multirate Engineering Solution

To bypass the limitations of sharp analog filters, modern high-speed architectures use **Oversampling** paired with **Multirate Digital Signal Processing**.

### Oversampling

Operating the ADC core at a sampling frequency significantly higher than the Nyquist rate of the target signal widens the first Nyquist zone. This creates a wide spectral buffer between the signal of interest and the point where aliasing occurs.

As a result, the strict transition requirements of the analog front-end filter are greatly relaxed, allowing the use of simpler, lower-cost analog circuits. Any out-of-band noise or signals that pass through the relaxed analog filter can then be removed in the digital domain using sharp, highly stable digital filters.

### The Computational Bottleneck

While oversampling simplifies the analog hardware, it presents a challenge for the digital domain: streaming data into the FPGA fabric at an unnecessarily high sampling rate increases the clock speed requirements and computational load for all subsequent processing stages.

### Multirate Operations

Multirate processing resolves this bottleneck by dynamically altering the data sampling rate at different stages of the processing chain. This approach ensures that the analog interfaces (ADCs and DACs) operate at high rates to simplify filtering, while the core digital signal processing algorithms (such as qubit state discrimination) run at lower rates near the Nyquist minimum to optimize power and hardware efficiency.

```
       +------------+   fs_high   +--------------------+   fs_low   +---------------------+
-----> |  High-Speed| ----------> | MULTIRATE DIGITAL  | ---------> | Core Digital Blocks |
       |  ADC Core  |             | FILTERING ENGINE   |            | (State Analysis)    |
       +------------+             +--------------------+            +---------------------+

```

---

## 3. Core Multirate Mechanisms and Scenarios

Multirate operations primarily change a system's sampling rate through integer scaling factors:

* **Decimation (Factor $M$):** Reduces the sampling frequency by an integer factor $M$ (e.g., downsampling a $10\ \text{MHz}$ input stream by a factor of $M=2$ yields a $5\ \text{MHz}$ output stream).
* **Interpolation (Factor $L$):** Increases the sampling frequency by an integer factor $L$ (e.g., upsampling a $10\ \text{MHz}$ baseband stream by a factor of $L=4$ yields a $40\ \text{MHz}$ output stream).

### Common Multirate Scenarios

* **Asynchronous Path Rate Matching:** Aligns two digital signal streams operating at different native sampling rates so they can be combined or mixed. For instance, in an audio mixing console, an $8\ \text{kHz}$ voice path is upsampled by a factor of 6 ($L=6$) to match a $48\ \text{kHz}$ music background path.
* **Converter Interface Matching:** Matches the lower internal processing rates of an FPGA-based waveform generator to the multi-giga-sample operating rates of an RF-DAC core.
* **Analog Filter Optimization:** Pairs with an oversampled ADC to handle the bulk of the anti-aliasing task via software-defined digital filters, keeping the physical analog hardware requirements minimal.

---

## 4. Oversampled ADC and Decimation Pipelines

### Decimation Framework

Decimation is the two-step architectural process of reducing a signal's sampling rate from an initial frequency $f_s$ down to a lower decimation frequency $f_d$:

$$f_d = \frac{f_s}{M}$$

To reduce the rate without losing information, the decimation pipeline must perform two sequential tasks:

```
                  +-------------------------------------------------+
                  |               DECIMATION PIPELINE               |
      fs_high     |  +-------------------+     +-----------------+  |      fs_low
----------------> |  | Digital Anti-Alias| --> |   Downsampler   |  | -------------->
   Input Data     |  | Low-Pass Filter(H)|     | (Discard M-1)   |  |   Output Data
                  |  +-------------------+     +-----------------+  |
                  +-------------------------------------------------+

```

1. **Digital Anti-Aliasing Filtering:** A digital low-pass filter (typically implemented as a Finite Impulse Response, or FIR, filter) attenuates all high-frequency components above $\frac{f_d}{2}$ before the sampling rate is altered.
2. **Downsampling:** The downsampling block reduces the sampling rate by keeping only every $M^{\text{th}}$ incoming sample and discarding the $M-1$ intermediate samples.

### Frequency and Time Domain Decimation Example

Consider an oversampled ADC input stream $x[k]$ with a sampling frequency $f_s = 2\ \text{GHz}$ and a target signal bandwidth limited to $480\ \text{MHz}$.

#### 1. The Conventional Approach

If the system uses a standard $1\ \text{GHz}$ ADC, any signal or noise in the $520\ \text{MHz}$ to $1\ \text{GHz}$ band will alias directly into the $0 - 480\ \text{MHz}$ target band. Preventing this requires an analog filter with an extremely sharp transition band spanning just $40\ \text{MHz}$ (from $480\ \text{MHz}$ to $520\ \text{MHz}$), which is difficult and costly to build.

#### 2. The Oversampled and Decimated Approach

Operating the ADC at $f_s = 2\ \text{GHz}$ widens the primary Nyquist zone up to $1\ \text{GHz}$, allowing the use of a relaxed analog filter.

After digitization, a digital decimation pipeline with a factor of $M=2$ reduces the sampling rate to $1\ \text{GHz}$.

* **The Filter Action:** The digital FIR filter attenuates the components in the upper half of the spectrum ($500\ \text{MHz}$ to $1\ \text{GHz}$).
* **The Downsampler Action:** The downsampler drops every second sample, increasing the time interval between the remaining samples by a factor of 2 ($t_d = 2 t_s$).

In the time domain, the sample indices shift from the high-rate notation $k$ to a lower-rate index $n$, reflecting the reduced data volume. In the frequency domain, the spectral images shift closer together. However, because the digital filter cleared the upper frequency band before downsampling, no aliasing occurs within the final $0 - 500\ \text{MHz}$ band.

---

## 5. Interpolated DAC Pipelines

At the transmit interface, a similar dual-stage approach is used to simplify the analog reconstruction or image-rejection filters at the output of a Digital-to-Analog Converter (DAC).

### Interpolation Framework

Interpolation increases the sampling rate of a digital signal from an initial baseband frequency $f_s$ up to an oversampled frequency $f_u$ by an integer factor $L$:

$$f_u = L \cdot f_s$$

The interpolation pipeline performs these operations in the reverse order of the decimation pipeline:

```
                  +-------------------------------------------------+
                  |              INTERPOLATION PIPELINE             |
       fs_low     |  +-------------------+     +-----------------+  |     fs_high
----------------> |  |    Upsampler      | --> |  Digital Image  |  | -------------->
   Input Data     |  | (Insert L-1 Zeros)|     | Rejection Filter|  |   Output Data
                  |  +-------------------+     +-----------------+  |
                  +-------------------------------------------------+

```

1. **Upsampling:** The upsampler inserts $L-1$ zero-valued samples between each pair of original incoming samples, increasing the data rate by a factor of $L$.
2. **Digital Image Rejection Filtering:** This digital low-pass stage removes the unwanted high-frequency spectral images generated by the upsampling step. These images appear at integer multiples of the original sampling rate up to the new folding frequency $\frac{f_u}{2}$.

### Time and Frequency Domain Interpolation Example

Consider a baseband system driving an output carrier at $10\ \text{MHz}$ with a native digital input update rate $f_s = 30\ \text{MSps}$.

#### 1. Un-Interpolated Output Spectrum

Without interpolation, the DAC synthesizes the primary $10\ \text{MHz}$ tone alongside its first high-frequency spectral image located at:

$$f_{\text{image}} = f_s - f_{\text{out}} = 30\ \text{MHz} - 10\ \text{MHz} = 20\ \text{MHz}$$

To isolate the $10\ \text{MHz}$ tone, the analog reconstruction filter must feature a very sharp transition band that starts immediately at $10\ \text{MHz}$ and reaches full attenuation by $20\ \text{MHz}$.

#### 2. Interpolated Output Spectrum ($L=2$)

Passing the digital signal through an interpolation pipeline with a factor of $L=2$ increases the internal update rate to $60\ \text{MSps}$.

* **Time-Domain Insertion:** The upsampler inserts one zero-valued sample between each original sample.
* **Digital Filtering Action:** The digital low-pass filter processes these zero-filled streams, interpolating the intermediate points to smooth out the waveform. This fills in the zero-valued gaps with correct intermediate amplitudes.
* **Spectral Shift:** The digital filter suppresses the intermediate image at $20\ \text{MHz}$. The first physical image produced at the DAC output now appears at:

$$f_{\text{image\_new}} = f_u - f_{\text{out}} = 60\ \text{MHz} - 10\ \text{MHz} = 50\ \text{MHz}$$

The analog reconstruction filter's transition window is now significantly wider, spanning from $10\ \text{MHz}$ up to $50\ \text{MHz}$. This allows designers to use a simpler, lower-order analog filter while maintaining high signal purity.

---

## 6. Structural Comparison Matrix

$$\begin{array}{lll}
\hline
\textbf{Design Vector} & \textbf{Decimation Pipeline (ADC Side)} & \textbf{Interpolation Pipeline (DAC Side)} \\ \hline
\text{Primary Purpose} & \text{Reduces data rate to save computing power} & \text{Increases data rate to simplify analog filters} \\
\text{Integer Scaling Factor} & M & L \\
\text{Sampling Frequency Mapping} & f_d = \frac{f_s}{M} & f_u = L \cdot f_s \\
\text{Order of Internal Operations} & \text{1. Digital Low-Pass Filter} & \text{1. Zero-Insertion Upsampler} \\
& \text{2. Downsampler (Keep 1 of } M) & \text{2. Digital Low-Pass Filter} \\
\text{Filter Functional Assignment} & \text{Anti-Aliasing (Clears upper bands)} & \text{Image Rejection (Smooths out zeros)} \\
\text{Time Domain Impact} & \text{Discards unnecessary data samples} & \text{Interpolates intermediate voltage values} \\ \hline
\end{array}$$