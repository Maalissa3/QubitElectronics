# Coherent Manipulation and Characterization of Semiconductor Spin Qubits

**Mariagrazia Graziano** *mariagrazia.graziano@polito.it* Politecnico di Torino – VLSI Group

---

## 1. Core Qubit Operations: The Control Cycle

Scaling a semiconductor-based quantum processor requires three highly coordinated, repeatable steps executed via a tight digital-to-analogue hardware feedback loop:

1. **Initialize:** Place the two-level quantum system into a known, highly pure ground state (typically $|0\rangle$).
2. **Manipulate:** Apply coherent electromagnetic or electrostatic perturbations to perform single- or multi-qubit gate operations.
3. **Readout:** Determine the final projection state of the qubit (measuring the $|1\rangle$ state probability) using spin-to-charge conversion techniques like the Elzerman method.
4. **Loop:** Reset and repeat the sequence (`GOTO START`) across thousands of identical runs to construct an accurate statistical quantum state tomography.

---

## 2. Electron Spin Resonance (ESR) and the Rabi Experiment

The **Rabi Experiment** is the fundamental diagnostic tool used to demonstrate the coherent time-evolution and oscillatory control of a two-level quantum system between its $|0\rangle$ and $|1\rangle$ states.

### Electromagnetic Wave Generation

To drive transitions within a quantum dot, an alternating current (AC) is routed through an on-chip microwave transmission line placed immediately adjacent to the dot structure. This AC excitation generates a localized transverse oscillating magnetic field ($\vec{B}_{\text{AC}}$) perpendicular to the primary static sorting magnetic field ($\vec{B}_0$).

### Magnetic Resonance Condition

According to the principles of magnetic resonance, the oscillating field $\vec{B}_{\text{AC}}$ along a single physical axis can be mathematically decomposed into two counter-rotating circular magnetic field vectors: one rotating clockwise and the other counter-clockwise on the Bloch sphere's equatorial plane.

* **Precession:** The electron spin precesses around the main static longitudinal axis ($\vec{B}_0$) at its native Larmor frequency, determined directly by its split Zeeman energy:

$$\Delta E_Z = h \nu_{\text{Larmor}}$$


* **Resonance:** When the microwave drive frequency matches $\nu_{\text{Larmor}}$, the counter-rotating component that moves in the opposite direction averages out to zero over time. However, the component rotating in unison with the precessing spin becomes **stationary** relative to the qubit's rotating frame of reference.
* **The Frame Shift:** In this rotating frame, the static $\vec{B}_0$ vector is effectively canceled out, leaving only a stable, fixed magnetic field component pointing along a transverse axis. The spin vector immediately begins a secondary precession around this new axis, driving a deterministic angular rotation over the surface of the Bloch sphere.

### The "Spin-Dance" Analogy

To visualize this dual-precession phenomenon, consider the following physical analogy:

> **The Coordinate Setup:** Stand vertically to represent the longitudinal static magnetic field axis ($\vec{B}_0$). Keep your elbow tucked against your side and extend your hand outward at an angle; this arm represents the qubit's spin vector.
> **The Baseline Motion:** Spin around on your feet at a constant speed. This motion emulates the native Larmor precession of the qubit around the static field.
> **Introducing the Drive:** Extend your other arm horizontally to represent the transverse AC magnetic field component. If you spin your body at the exact same rate as this external drive field, your horizontal arm will look completely stationary from the perspective of your precessing arm.
> **The Double Precession:** Now, rotate your precessing arm up and down around that "stationary" horizontal arm while your entire body continues to spin on its feet. The resulting path traced by your hand through space forms a complex, overlapping **spiral pattern**.

---

## 3. Rabi Oscillations and Environmental Decoherence

When the resonant microwave pulse is applied for a controlled duration (called the **burst time**) and followed by an Elzerman readout, the probability of finding the qubit in the excited $|1\rangle$ state oscillates as a perfect sinusoidal function of time.

### Controlling Rotation Speed

The frequency of this state-to-state oscillation—the **Rabi frequency ($\Omega_{\text{Rabi}}$)**—is directly proportional to the amplitude (power) of the applied AC control pulse. Higher drive power rotates the spin vector more rapidly across the Bloch sphere.

### Decoherence and Environmental Noise

In real physical systems, these sinusoidal oscillations exhibit a characteristic exponential envelope decay. This signal damping is caused by **quantum decoherence**, where the qubit loses phase information due to interactions with its local environment.

* **The GaAs Bottleneck:** Gallium Arsenide ($\text{GaAs}$) heterostructures suffer from severe decoherence because every native isotope of Gallium and Arsenic possesses a non-zero nuclear spin. These surrounding nuclear spins flip randomly over time, creating a noisy, fluctuating local magnetic field (the Overhauser field). This fluctuation causes the qubit's resonance frequency to drift stochastically, throwing the control pulses out of resonance.
* **Isotopic Purification via Silicon-28:** To suppress this primary decoherence path, architectures use **Silicon-28 ($\text{}^{28}\text{Si}$)**. Silicon can be isotopically purified to isolate the $\text{}^{28}\text{Si}$ isotope, which has an absolute **zero nuclear spin**. Eliminating the local nuclear magnetic background removes the main source of frequency drift, extending qubit coherence times by orders of magnitude.

$$\begin{array}{llll}
\hline
\textbf{Substrate Material} & \textbf{Nuclear Spin Profile} & \textbf{Experiment Type} & \textbf{Typical Coherence Time } (T_2^*) \\ \hline
\text{GaAs / AlGaAs} & \text{High-density active nuclear spins} & \text{Ramsey Fringe} & \sim 10\ \text{ns} \\
\text{Purified Silicon-28} & \text{Zero nuclear spin background} & \text{Ramsey Fringe} & \sim 120\ \mu\text{ms} \\ \hline
\end{array}$$

---

## 4. Case Study: Cryo-CMOS Qubit Control via Intel Horse Ridge

The **Horse Ridge** cryogenic CMOS integrated circuit (developed by Intel and TU Delft) represents a major step forward in replacing bulky, room-temperature room instrumentation with a compact, silicon-based controller that operates directly inside the dilution refrigerator.

```
       +-------------------------------------------------------------+
       |                  HORSE RIDGE CRYO-CMOS SOC                  |
       |  +---------------------------+   +-----------------------+  |
       |  |  Low-Frequency DC Bias    |   |  Direct RF Synthesis  |  |
       |  |  (Plunger Gate Tuning)    |   |  (13.4 & 17.5 GHz)    |  |
       |  +---------------------------+   +-----------------------+  |
       +-------------------------------------------------------------+
                       |                                  |
    DC Potentials (LP, RP)                Resonant Microwave Bursts
                       v                                  v
       +-------------------------------------------------------------+
       |                  SILICON-28 QUANTUM WELL                    |
       |     [Gate LP]                              [Gate RP]        |
       |    (Electron 1) <----Exchange Coupling----> (Electron 2)     |
       |  ---------------------------------------------------------  |
       |              On-Chip Micromagnet Array (\vec{B}_0)           |
       +-------------------------------------------------------------+

```

### Core Architecture and Setup

* **The Device Under Test:** A double quantum dot defined inside a **$10\ \text{nm}$ thick $\text{}^{28}\text{Si/SiGe}$ quantum well**.
* **Electrostatic Tuning:** Two main surface plunger gates—**LP (Left Plunger)** and **RP (Right Plunger)**—apply stable DC potentials to accumulate and isolate exactly one single electron underneath each gate electrode.
* **Magnetic Configuration:** An external static magnetic field of $380\ \text{mT}$ is paired with an on-chip **micromagnet array** fabricated directly on top of the double-dot structure. This micromagnet creates a controlled magnetic field gradient, inducing a distinct Zeeman splitting for each qubit.

### Characterizing Rabi Performance via Horse Ridge

To characterize the single-qubit gate capabilities of the Horse Ridge system, engineers use a simple test sequence:

1. **Initialize:** Tune the dot gates to settle the electron spin into the lower $|0\rangle$ ground state.
2. **Excite:** Apply a sharp, rectangular microwave control pulse generated directly by the SoC's high-frequency output lines.
3. **Measure:** Read out the resulting spin projection state using the Elzerman method to evaluate the final $|1\rangle$ state probability.

By sweeping the duration of the microwave pulse, the system maps out clean Rabi oscillations. Testing across the chip's dual RF channels yields precise operational metrics:

* **$\text{RF}_{\text{low}}$ Output Channel:** Achieves a **$1\ \text{MHz}$ Rabi frequency** operating at a carrier frequency of **$13.4\ \text{GHz}$**.
* **$\text{RF}_{\text{high}}$ Output Channel:** Achieves a **$400\ \text{kHz}$ Rabi frequency** operating at a carrier frequency of **$17.5\ \text{GHz}$**.

> **Improving Readout Visibility:** The raw signal contrast (readout visibility) can be degraded by high-frequency electrical noise coupled onto the device's control gates. To suppress this out-of-band noise and optimize state discrimination, a discrete analog **bandpass filter with a $2\ \text{GHz}$ wide passband** is integrated directly into the chip's output measurement path.

---

## 5. Dual-Axis Coherent Control: The Ramsey Experiment

While a standard Rabi sequence validates single-axis rotations, proving full, arbitrary control over a qubit requires demonstrating phase-coherent rotation capabilities across multiple orthogonal axes. This is achieved using a **Ramsey-style experiment**.

### The Multi-Pulse Ramsey Sequence

The Ramsey protocol sequences multiple control pulses separated by a precise time interval to track phase evolution:

```
  Initialize       X-Axis Rotation          Z-Axis Rotation          X-Axis Rotation         Readout
   |0> State       (Pi/2 Pulse)           (Variable Phase Angle)      (Pi/2 Pulse)         State Prob.
     |                  |                          |                       |                    |
     v                  v                          v                       v                    v
  [ |0> ] -------> [ R_X(pi/2) ] -----------> [ R_Z(theta) ] -------> [ R_X(pi/2) ] -------> [ Measure ]

```

1. **First $\frac{\pi}{2}$ Pulse ($R_X(\frac{\pi}{2})$):** Applies a resonant rectangular microwave pulse along the $X$-axis. This turns the spin vector by exactly $90^\circ$, rotating it from the longitudinal pole down onto the Bloch sphere's equatorial plane into a superposition state.
2. **Phase Rotation ($R_Z(\theta)$):** Rotates the spin vector around the longitudinal $Z$-axis by a variable angle $\theta$, tracking the phase evolution between the qubit and the reference clock.
3. **Second $\frac{\pi}{2}$ Pulse ($R_X(\frac{\pi}{2})$):** Applies an identical $X$-axis rotation pulse. This second turn translates the accumulated phase angle back into a measurable longitudinal state population difference.

### Physical Realization of Control Axes

* **$X$-Axis and $Y$-Axis Rotations:** Driven physically by pulsing a rectangular microwave burst (set to $13.7\ \text{GHz}$ in this processor framework). The duration of the microwave pulse is directly proportional to the intended rotation angle.
* **$Z$-Axis Rotations:** Rather than applying a physical pulse, $Z$-axis phase rotations are implemented via **Virtual $Z$-Gates**. The system updates the reference phase clock inside the controller's digital signal processor with a precise phase offset ($\theta$). This digital offset shifts the phase of all subsequent $X$ and $Y$ microwave pulses, tracking the qubit's phase evolution without introducing hardware overhead or pulse calibration errors.

### Coherent Validation Metrics

Plotting the measured $|1\rangle$ state probability against a $Z$-gate phase sweep from $0^\circ$ to $360^\circ$ maps out a clean, repeating **cosinusoidal fringe pattern**.

The close alignment between this experimental data and theoretical predictions confirms that the controller can manipulate the electron's spin state with high phase coherence, validating its ability to execute any arbitrary single-qubit quantum gate.