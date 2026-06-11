Here is the completely unified, comprehensive reference manual. This version explicitly preserves every introductory explanation, step-by-step point, and practical example from your original text, seamlessly weaving them into the deep mathematical, physical, and structural breakdowns.

---

# Signal Representation

## 1. Why We Need Multiple Representations

Signals are physical quantities (such as time-varying voltages or currents) generated, transmitted, processed, and measured in different domains. A single representation rarely suits all engineering tasks.

```
   Physical Domain              Mathematical Representation          Engineering Application
+---------------------+        +-----------------------------+        +-------------------------+
| Time Domain         | -----> | s(t) = A(t)cos(ωt + φ)      | -----> | Power/Amplifier Design  |
+---------------------+        +-----------------------------+        +-------------------------+
| Frequency Domain    | -----> | S(ω) = F{s(t)}              | -----> | Spectrum/Filter Design  |
+---------------------+        +-----------------------------+        +-------------------------+
| Complex Baseband    | -----> | u(t) = I(t) + jQ(t)         | -----> | DSP/SDR Algorithms      |
+---------------------+        +-----------------------------+        +-------------------------+

```

* **Time-Domain:** Essential for physical hardware design. Real voltages in engineering and communications are time-varying waveforms that can be positive or negative. Power amplifiers, Analog-to-Digital Converters (ADCs), and transmission media respond directly to these instantaneous values.
* **Frequency-Domain:** Necessary for regulatory compliance (spectral masks), channel allocation, and filter design. It isolates the distribution of energy across the spectrum, clarifying parameters like absolute bandwidth.
* **Complex/Baseband Domain:** Different views (time, frequency, complex/baseband, phasor) clarify bandwidth, phase, and modulation while simplifying design and DSP implementation. By decoupling the information-bearing envelope from the high-frequency carrier, it drastically reduces computational complexity.
* **Core Modern Applications:** This dual-representation paradigm is foundational to:
* **Software Defined Radios (SDR):** Where modulation, filtering, and synchronization occur entirely in software via complex numeric arrays.
* **Quantum Computing (Microwave Qubit Control):** Where microwave pulses must be shaped with nanosecond precision in both amplitude and phase to manipulate the state transitions of superconducting qubits.



---

## 2. Real vs. Complex (I/Q) Representation

A real narrowband carrier or physical passband signal localized around a carrier frequency $\omega_c = 2\pi f_c$ can be expressed in its time-domain real form as:

$$x(t) = A(t) \cos(\omega_c t + \phi(t))$$

Where $A(t)$ represents the instantaneous amplitude modulation and $\phi(t)$ represents the instantaneous phase modulation. In physical receivers, these signals are transformed into a complex form to make analysis and hardware design easier to handle. Using the trigonometric identity $\cos(\alpha + \beta) = \cos\alpha\cos\beta - \sin\alpha\sin\beta$, we can expand this expression:

$$x(t) = A(t)\cos(\phi(t))\cos(\omega_c t) - A(t)\sin(\phi(t))\sin(\omega_c t)$$

By defining the **In-phase ($I(t)$)** and **Quadrature ($Q(t)$)** components as:

* **$I(t)$**: The in-phase component, scaling the cosine carrier: $I(t) = A(t)\cos(\phi(t))$
* **$Q(t)$**: The quadrature component, scaling the sine carrier: $Q(t) = A(t)\sin(\phi(t))$

The real passband signal simplifies to a linear combination of two orthogonal carrier waves:

$$x(t) = I(t)\cos(\omega_c t) - Q(t)\sin(\omega_c t)$$

This complex notation makes quadrature modulators and demodulators straightforward to describe. Using Euler's formula, we wrap these components into an equivalent complex (analytic) representation that utilizes a **Complex Baseband Signal / Complex Envelope** $u(t) = I(t) + jQ(t)$:

$$x(t) = \text{Re}\left\{ u(t) e^{j\omega_c t} \right\} = \text{Re}\left\{ (I(t) + jQ(t)) e^{j \omega_c t} \right\}$$

### Structural Properties of the I/Q Vector Space

* **Orthogonality:** The carriers $\cos(\omega_c t)$ and $\sin(\omega_c t)$ are orthogonal ($90^\circ$ apart in phase) over a carrier period $T_c = \frac{2\pi}{\omega_c}$:
$$\int_{0}^{T_c} \cos(\omega_c t)\sin(\omega_c t) \, dt = 0$$


Because they are completely orthogonal, they permit independent amplitude and phase control without interfering with each other.
* **Geometric Mapping:** This structural independence allows $I(t)$ and $Q(t)$ to map directly onto a two-dimensional Cartesian plane (Constellation Diagram), functioning as the physical implementation of a complex signal.

---

## 3. Analytic Signal and Hilbert Transform

For a real-valued physical signal $s(t)$, the **Analytic Signal** $s_a(t)$ is a complex-valued extension used for advanced processing. It eliminates all negative-frequency content while preserving the positive-frequency spectrum. It is defined mathematically as:

$$s_a(t) = s(t) + j \mathcal{H}\{s(t)\}$$

Where $\mathcal{H}\{\cdot\}$ is the **Hilbert Transform**, defined as the convolution of $s(t)$ with the impulse response $h(t) = \frac{1}{\pi t}$:

$$\mathcal{H}\{s(t)\} = s(t) * \frac{1}{\pi t} = \frac{1}{\pi} \int_{-\infty}^{\infty} \frac{s(\tau)}{t - \tau} \, d\tau$$

### Frequency-Domain Operation

In the frequency domain, the Hilbert transform acts as an ideal $-90^\circ$ phase shifter for positive frequencies and a $+90^\circ$ phase shifter for negative frequencies:

$$H(\omega) = \mathcal{F}\left\{\frac{1}{\pi t}\right\} = -j \text{sgn}(\omega) = \begin{cases} -j = e^{-j\pi/2}, & \omega > 0 \\ 0, & \omega = 0 \\ +j = e^{j\pi/2}, & \omega < 0 \end{cases}$$

Consequently, the Fourier transform of the analytic signal $S_a(\omega)$ becomes:

$$S_a(\omega) = S(\omega) + j [-j \text{sgn}(\omega)] S(\omega) = S(\omega) [1 + \text{sgn}(\omega)] = \begin{cases} 2S(\omega), & \omega > 0 \\ S(0), & \omega = 0 \\ 0, & \omega < 0 \end{cases}$$

As a result, $s_a(t)$ contains only positive-frequency content. Any narrowband real signal can be written as the real part of this analytic signal, linking back to the complex envelope:

$$s(t) = \text{Re}\{ s_a(t) \} = \text{Re}\{ u(t) e^{j \omega_c t} \}$$

---

## 4. Euler’s Formula and Complex Spectra

### Mathematical Foundations

The foundation of this complex representation relies directly on Euler's formula:

* $e^{j\omega t} = \cos(\omega t) + j\sin(\omega t)$
* $e^{-j\omega t} = \cos(\omega t) - j\sin(\omega t)$

From these expressions, we derive the standard exponential identities for basic sinusoids:

* $2\cos(\omega t) = e^{j\omega t} + e^{-j\omega t}$
* $2j\sin(\omega t) = e^{j\omega t} - e^{-j\omega t}$

### Real vs. Complex Signals in the Frequency Domain

* **One-Sided Spectrum:** A real signal is usually illustrated practically using a one-sided spectrum, where only positive frequencies are displayed because the negative side contains duplicate information.
* **Two-Sided Spectrum:** A complex signal possesses a two-sided spectrum with independent components on both the positive and negative frequency axes.
* **Conceptual Interpretation:** The terms "positive" and "negative frequency" are best understood physically as **positive and negative complex exponentials** rotating in opposite directions in the complex plane.

### Benefits of Complex Notation (Algebraic Simplicity)

* **Real Trigonometric Products:** Multiplying real signals yields unwieldy expansions:
$$\cos A \cos B = \frac{1}{2}[\cos(A+B)+\cos(A−B)]$$


This splits the energy and generates undesired high-frequency image terms that require extra hardware filtering to remove.
* **Complex Exponentials:** Complex multiplication simplifies directly to exponent addition:
$$e^{jA} e^{jB} = e^{j(A+B)}$$


Multiplication becomes a clean frequency translation without generating stray image sidebands.
* **Frequency Shift Theorem:** If $S(\omega)$ is the spectrum of a baseband signal $u(t)$, then multiplying it by a complex carrier shifts its entire spectrum cleanly along the frequency axis:
$$\mathcal{F}\{ u(t) e^{j\omega_c t} \} = U(\omega − \omega_c)$$



---

## 5. Frequency Domain Examples & Symmetry

### Example 1: Sum of Three Ideal Tones

Consider a multi-tone real signal defined by:

$$s_1(t) = 10\cos(2\pi 100 t) + \cos(2\pi 200 t) + 4\cos(2\pi 300 t)$$

* **Time-Domain View:** In the time domain, plotting $s_1(t)$ yields a complex, overlapping waveform where individual frequency content is difficult to isolate visually.
* **Frequency-Domain View:** In the frequency domain, the signal is easy to interpret as three discrete sinusoidal components. Using the exponential expansion $\cos(\omega t) = \frac{e^{j\omega t} + e^{-j\omega t}}{2}$, the complex two-sided spectrum displays symmetric component pairs at $\pm 100\text{ Hz}$, $\pm 200\text{ Hz}$, and $\pm 300\text{ Hz}$. These symmetric components combine to form the real cosine signal, and the ideal imaginary spectrum is completely zero.

### Example 2: Sum of Three Tones with Phase Shifts

Let us introduce arbitrary phase shifts to the standard cosine tones from the first example:

$$s_2(t) = 10\cos(2\pi 100t + \pi/4) + \cos(2\pi 200t + \pi/6) + 4\cos(2\pi 300t)$$

* **Time-Domain Changes:** In the time domain, the phase shifts alter the alignment of the peaks, changing the shape of the overall waveform. However, the *magnitude spectrum* remains exactly the same as it was for $s_1(t)$.
* **Phase Spectrum Changes:** The phase spectrum changes to reflect the added offsets. Expanding this with Euler's formula reveals complex coefficients:

$$10\cos\left(2\pi 100t + \frac{\pi}{4}\right) = 5e^{j\pi/4}e^{j2\pi 100t} + 5e^{-j\pi/4}e^{-j2\pi 100t}$$

Evaluating the complex spectral coefficients $c_n$ for each frequency step reveals non-zero real and imaginary parts:

* $c_{100} = 5e^{j\pi/4} = \frac{5\sqrt{2}}{2} + j\frac{5\sqrt{2}}{2}, \quad c_{-100} = 5e^{-j\pi/4} = \frac{5\sqrt{2}}{2} - j\frac{5\sqrt{2}}{2}$
* $c_{200} = 0.5e^{j\pi/6} = \frac{\sqrt{3}}{4} + j\frac{1}{4}, \quad c_{-200} = 0.5e^{-j\pi/6} = \frac{\sqrt{3}}{4} - j\frac{1}{4}$
* $c_{300} = 2e^{j0} = 2, \quad c_{-300} = 2e^{-j0} = 2$

### Key Symmetry Observations

* **Conjugate Symmetry:** For all real-valued physical signals, the two-sided complex spectrum is strictly **conjugate symmetric** ($S(-\omega) = S^*(\omega)$). This means:
* The real part of the spectrum is **even-symmetric** ($\text{Re}\{S(-\omega)\} = \text{Re}\{S(\omega)\}$)
* The imaginary part of the spectrum is **odd-symmetric** ($\text{Im}\{S(-\omega)\} = -\text{Im}\{S(\omega)\}$)


* **Phase Representation:** Because phase information is naturally embedded within the relationship between these real and imaginary components, a separate phase spectrum is not always mathematically required.
* **Utility:** Complex two-sided spectra are especially useful in quadrature communications because they make phase shifts and directional frequency rotations easy to track and interpret.

---

## 6. Modulation Mechanics

### Basic Concept of Modulation

A standard sinusoidal carrier wave can be mathematically described by its basic parameters:

$$v(t) = A \sin(2\pi f t + \phi)$$

Modulation alters one or more of these core parameters—**amplitude**, **frequency**, or **phase**—of a high-frequency carrier wave according to a lower-frequency message signal $m(t)$. This shifts the information to a higher frequency band so it can propagate effectively through physical transmission media such as air, optical fiber, or coaxial cables.

### Amplitude Modulation (AM)

In standard Amplitude Modulation, the carrier's amplitude varies linearly with the message signal. Let a message signal be $m(t) = A_m \cos(2\pi f_m t)$ and the unmodulated carrier be $c(t) = A_c \cos(2\pi f_c t)$. The combined AM signal is expressed as:

$$s(t) = [A_c + A_m \cos(2\pi f_m t)] \cos(2\pi f_c t)$$

Using the trigonometric identity $\cos A \cos B = \frac{1}{2}[\cos(A+B) + \cos(A-B)]$, we rewrite the AM signal in its explicit spectral form:

$$s(t) = A_c \cos(2\pi f_c t) + \frac{A_m}{2}\cos(2\pi(f_c+f_m)t) + \frac{A_m}{2}\cos(2\pi(f_c-f_m)t)$$

* **AM Frequency Profile:** This produces a discrete carrier at $f_c$, a Lower Sideband (LSB) at $f_c - f_m$, and an Upper Sideband (Reflecting $f_c + f_m$).
* **Bandwidth Inefficiency:** Standard AM is inefficient because it requires roughly twice the baseband bandwidth ($2f_m$) to transmit a single-ended message, duplicating information symmetrically on both sides of the carrier.

### Quadrature Modulation

Quadrature modulation resolves this inefficiency by using two orthogonal carriers simultaneously: $\cos(\omega t)$ for the In-phase ($I$) branch and $\sin(\omega t)$ for the Quadrature ($Q$) branch.

Because these two carrier waves are $90^\circ$ apart in phase, they do not interfere with each other during transmission. This allows both independent signals to occupy the exact same frequency band simultaneously, doubling the spectral efficiency relative to standard AM.

---

## 7. I/Q Modulation and Demodulation Linearity

In real-world communications, I/Q modulation is more than a mathematical convenience: it is how physical transmitters and receivers operate.

### Upconversion Time-Domain Form (Transmitter)

The transmitted hardware signal maps directly to the real and imaginary parts of the complex baseband signal $u(t) = g_1(t) + j g_2(t)$. It can be written as:

$$y(t) = g_1(t)\cos(2\pi f_c t) - g_2(t)\sin(2\pi f_c t)$$

Where:

* $g_1(t)$ represents the continuous **In-Phase ($I$) channel** stream.
* $g_2(t)$ represents the continuous **Quadrature ($Q$) channel** stream.
* $f_c$ is the targeted carrier frequency.

Expressed compactly in complex notation, this matches our foundational framework:

$$y(t) = \text{Re}\{u(t)e^{j2\pi f_c t}\}$$

By changing $g_1(t)$ ($I(t)$) and $g_2(t)$ ($Q(t)$) over time, the resulting output carries amplitude changes, phase changes, or complex digital modulations (such as QAM or QPSK). For example, if $I$ and $Q$ are driven with equal static values, the resulting passband signal exhibits a predictable phase shift of exactly $45^\circ$.

### I/Q Demodulation (Receiver Architecture)

At the receiver, the incoming signal $r(t)$ is split into two parallel analog processing branches. To decode the signal, it is multiplied by two synchronized local oscillators ($0^\circ$ cosine for the $I$ branch, $90^\circ$ sine for the $Q$ branch) and passed through low-pass filters:

```
                      +---> [ Mixer ] ---> LPF ---> I(t) = 0.5 * g1(t)
                      |         ^
                      |         | cos(2π fc t)
r(t) ---+-------------+   [Local Osc]
        |             |         |
        |             |         v -90° Phase Shift
        |             +---> [ Mixer ] ---> LPF ---> Q(t) = 0.5 * g2(t)
        |                       |
        +-----------------------+ sin(2π fc t)

```

#### Mathematical Proof of Separation (Cross-Talk Elimination)

Assuming a noise-free transmission where $r(t) = g_1(t)\cos(\omega_c t) - g_2(t)\sin(\omega_c t)$:

1. **In-Phase Branch Multiplier:**
$$r(t)\cos(\omega_c t) = g_1(t)\cos^2(\omega_c t) - g_2(t)\sin(\omega_c t)\cos(\omega_c t)$$


Applying standard trigonometric identities ($\cos^2 \theta = \frac{1+\cos2\theta}{2}$ and $\sin\theta\cos\theta = \frac{\sin2\theta}{2}$):
$$r(t)\cos(\omega_c t) = \frac{1}{2}g_1(t) + \frac{1}{2}g_1(t)\cos(2\omega_c t) - \frac{1}{2}g_2(t)\sin(2\omega_c t)$$


Passing this through an ideal Low-Pass Filter ($\text{LPF}$) completely attenuates the high-frequency $2\omega_c$ components, isolating the $I$ data:
$$I(t) = \text{LPF}\{ r(t)\cos(\omega_c t) \} = \frac{1}{2}g_1(t)$$


2. **Quadrature Branch Multiplier:**
Multiply the incoming signal by $\sin(\omega_c t)$:
$$r(t)\sin(\omega_c t) = g_1(t)\cos(\omega_c t)\sin(\omega_c t) - g_2(t)\sin^2(\omega_c t)$$


Applying standard trigonometric identities ($\sin^2 \theta = \frac{1-\cos2\theta}{2}$):
$$r(t)\sin(\omega_c t) = \frac{1}{2}g_1(t)\sin(2\omega_c t) - \frac{1}{2}g_2(t) + \frac{1}{2}g_2(t)\cos(2\omega_c t)$$


Passing this through the ideal Low-Pass Filter removes the $2\omega_c$ terms, leaving the scaled $Q$ data:
$$Q(t) = \text{LPF}\{ r(t)\sin(\omega_c t) \} = -\frac{1}{2}g_2(t)$$



*(Note: Hardware paths flip the mixer sign or invert the digital array during processing to recover a positive $\frac{1}{2}g_2(t)$ stream).*

### Summary of Modulation vs. Demodulation

* **Modulation:** Adds information to a carrier wave, combining two separate streams ($I$ and $Q$) to alter the overall amplitude and phase over time.
* **Demodulation:** Recovers information from the carrier wave, separating the $I$ and $Q$ components so amplitude and phase can be precisely measured.

---

## 8. Positive/Negative Frequency and Image Rejection

Because real physical waveforms have symmetric spectra, a naive, single-ended downconversion process (multiplying a passband signal by a single real local oscillator $\cos(\omega_c t)$) folds the negative frequency mirror image directly on top of the target baseband signal. This results in irreversible distortion unless blocked by expensive analog front-end RF filters.

Analytic I/Q representation prevents this image folding. By maintaining both the $I$ and $Q$ signal branches throughout the receiver, the system preserves the phase relationship between the channels. This phase difference creates constructive interference for the target signal and destructive interference for the image frequency, cancelling out the unwanted image without needing steep analog front-end filtering.

---

## 9. Practical Notes for DSP and Hardware

### Complex Baseband Sampling (Nyquist Criteria)

If a real passband signal has an absolute bandwidth of $B$ centered at a high carrier frequency $f_c$, a direct real sampling architecture requires an ADC sampling rate greater than twice the highest frequency component:

$$f_s > 2\left(f_c + \frac{B}{2}\right)$$

By downconverting to complex baseband before digital conversion, the signal centers around $0\text{ Hz}$ and spans from $-\frac{B}{2}$ to $+\frac{B}{2}$. The Nyquist sampling rate for this complex bandwidth is reduced to:

$$f_s \ge B$$

This allows systems to use two lower-speed ADCs (sampling at $f_s \ge B$ for the separate $I$ and $Q$ paths) rather than a single, expensive ultra-high-speed ADC.

### Filtering and Calibration

* **Filtering:** Digital low-pass filters are designed and applied to the decoupled $I$ and $Q$ streams to remove the high-frequency image components and residual mixer products shown in section 7.
* **Phase/Amplitude Calibration:** In physical systems, analog components introduce **I/Q imbalance** (gain differences or minor phase deviations from a perfect $90^\circ$ shift). This imbalance causes image leakage, which modern communication front-ends estimate and correct using digital adaptive filters in DSP.

### Application: Superconducting Qubit Control

In quantum computing architectures, precise complex pulses (defined by their baseband envelope $u(t)$) are synthesized to shape the amplitude and phase of microwave lines. This control allows for exact quantum state manipulation.

During readout, the reflected or transmitted microwave signals are demodulated back into their complex baseband components ($I$ and $Q$). This allows the system to extract subtle amplitude and phase shifts, which are then used to determine the state of the qubit.

---

## 10. Summary Reference Sheet

### Core Formulation Guide

$$\begin{array}{ll}
\hline
\textbf{Signal Metric} & \textbf{Mathematical Formulation} \\ \hline
\text{Real Carrier Modulation} & x(t) = A(t)\cos(\omega_c t + \phi(t)) = I(t)\cos(\omega_c t) - Q(t)\sin(\omega_c t) \\
\text{Complex Baseband Envelope} & u(t) = I(t) + jQ(t) \\
\text{Analytic Signal Derivation} & s_a(t) = s(t) + j \mathcal{H}\{s(t)\} = u(t)e^{j\omega_c t} \\
\text{Frequency Shift Theorem} & \mathcal{F}\left\{ u(t)e^{j\omega_c t} \right\} = U(\omega - \omega_c) \\
\text{Ideal IQ Demodulation Output} & u(t) \approx 2 \cdot \text{LPF}\left\{ r(t)e^{-j\omega_c t} \right\} = I(t) + jQ(t) \\
\text{Conjugate Spectrum Symmetry} & S(-\omega) = S^*(\omega) \quad (\text{For real-valued time waveforms}) \\ \hline
\end{array}$$

### Identity Reference Checklist

* **Euler's Formula Expansion:** $e^{\pm j\omega t} = \cos(\omega t) \pm j\sin(\omega t)$
* **Sinusoidal Composition:** $\cos(\omega t) = \frac{e^{j\omega t} + e^{-j\omega t}}{2}, \quad \sin(\omega t) = \frac{e^{j\omega t} - e^{-j\omega t}}{2j}$
* **Standard AM Waveform:** $s(t) = A_c \cos(2\pi f_c t) + \frac{A_m}{2}\cos(2\pi(f_c+f_m)t) + \frac{A_m}{2}\cos(2\pi(f_c-f_m)t)$
* **Quadrature Passband Signal:** $y(t) = g_1(t)\cos(2\pi f_c t) - g_2(t)\sin(2\pi f_c t) = \text{Re}\{u(t)e^{j2\pi f_c t}\}$