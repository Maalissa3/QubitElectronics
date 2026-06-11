# Qubit control

## 1. Deep Dive into Readout in cQED

The core challenge in circuit Quantum Electrodynamics (cQED) is measuring a qubit without destroying its fragile quantum state.  
This is done by mapping the qubit state onto a microwave resonator physically coupled to it.

### The Jaynes-Cummings Hamiltonian

The interaction between a two-level atom (qubit) and a quantized electromagnetic field (resonator) is described by the Jaynes-Cummings Hamiltonian:

$$
H = \omega_r a^\dagger a + \frac{\omega_{01}}{2}\sigma_z - g\left(a^\dagger \sigma_- + a \sigma_+\right)
$$

- $\omega_r a^\dagger a$: energy of the photons in the resonator
- $\frac{\omega_{01}}{2}\sigma_z$: energy of the qubit
- $g(a^\dagger \sigma_- + a \sigma_+)$: interaction term, with coupling strength $g$

### The Dispersive Regime ($\Delta \gg g, \kappa$)

To avoid direct energy exchange between the qubit and the resonator, the system is operated in the dispersive regime, where they are highly detuned.

The detuning is:

$$
\Delta = \omega_{01} - \omega_r
$$

When $\Delta \gg g$, the Hamiltonian can be approximated as:

$$
H \approx (\omega_r + \chi \sigma_z)a^\dagger a + \frac{\omega_{01}}{2}\sigma_z
$$

where:

$$
\chi \approx \frac{g^2}{\Delta}
$$

This means the resonator frequency depends on the qubit state:

- if the qubit is in $|0\rangle$, the cavity responds at $\omega_r - \chi$
- if the qubit is in $|1\rangle$, the cavity responds at $\omega_r + \chi$

By sending a microwave probe tone at $\omega_r$ through the feedline, the reflected or transmitted signal acquires a phase shift. Measuring that phase shift reveals the qubit state.

---

## 2. The Purcell Effect and Purcell Filters

### The Problem: Purcell Decay

Because the resonator is coupled to a feedline with decay rate $\kappa$, the qubit can leak energy into the environment through the resonator. This unwanted relaxation is called Purcell decay:

$$
\gamma_p = \left(\frac{g}{\Delta}\right)^2 \kappa
$$

A larger $\kappa$ improves readout speed, but also increases $\gamma_p$. This creates a trade-off between measurement speed and qubit lifetime.

### The Solution: The Purcell Filter

A Purcell filter is placed between the resonator and the transmission line.

It acts as a frequency-selective bandpass filter:

- at $\omega_r$: transparent, allowing readout photons to pass
- at $\omega_q$: opaque, suppressing qubit relaxation

This allows fast readout without strongly reducing qubit coherence.

---

## 3. Controlling the Qubit

To perform quantum logic gates, the qubit state must be rotated on the Bloch sphere. For a transmon, there are two main control methods.

### Method A: Microwave Drive

This is used for $X$ and $Y$ rotations.

- A classical microwave voltage $V_d(t)$ is sent through a control line
- The line is coupled to the transmon by a small capacitor $C_d$
- The oscillating electric field couples to the qubit dipole moment and drives transitions

The coupling capacitor must be small to avoid strong dissipation into the $50\,\Omega$ control environment.

### Method B: Flux Control

This is used to change the qubit frequency and implement $Z$ gates or tune qubits into resonance.

A flux-tunable transmon uses a SQUID loop instead of a single Josephson junction. A magnetic flux $\Phi$ changes the Josephson energy $E_J$, and therefore the qubit frequency:

$$
\omega_q \propto \sqrt{8E_cE_J}
$$

---

## 4. Summary Table: Readout vs. Drive

| Feature | Readout (Measurement) | Drive (Control) |
|---|---|---|
| Objective | Determine whether the qubit is in $|0\rangle$ or $|1\rangle$ | Rotate the qubit state on the Bloch sphere |
| Coupling type | Dispersive capacitive coupling to a resonator | Direct capacitive or inductive coupling to a control line |
| Key physics | Jaynes-Cummings dispersive shift ($\chi$) | Dipole transitions or SQUID tuning |
| Protection mechanism | Purcell filter | Small coupling capacitor $C_d$ |

---

## 5. Mathematical Derivation of Microwave Control

### Step 1: Linearizing the Circuit Hamiltonian

Consider a classical transmon qubit connected to a microwave drive line via a small coupling capacitor $C_d$. The drive line applies a time-dependent voltage $V_d(t)$.

Near the ground state, the Josephson junction can be linearized and treated approximately as a harmonic oscillator. The Hamiltonian is:

$$
H = \frac{Q^2}{2C_\Sigma} + \frac{\Phi^2}{2L} + \frac{C_d}{C_\Sigma}V_d(t)Q
$$

where:

- $Q$ and $\Phi$ are charge and flux operators
- $C_\Sigma = C + C_d$ is the total capacitance
- the third term is the driving Hamiltonian

### Step 2: Quantization and Truncation

The charge operator is written as:

$$
Q = -iQ_{\text{zpf}}(a - a^\dagger)
$$

where:

$$
Q_{\text{zpf}} = \sqrt{\frac{\hbar}{2Z_0}}, \qquad Z_0 = \sqrt{\frac{L}{C_\Sigma}}
$$

Substituting into the Hamiltonian gives:

$$
H = \omega_q a^\dagger a - \frac{C_d}{C_\Sigma}V_d(t)iQ_{\text{zpf}}(a - a^\dagger)
$$

### Truncation to a Two-Level System

Because the transmon is weakly anharmonic, only the lowest two levels are kept:

$$
a \rightarrow \sigma_-, \qquad a^\dagger \rightarrow \sigma_+
$$

Using:

$$
\sigma_x = \sigma_+ + \sigma_-, \qquad \sigma_y = i(\sigma_- - \sigma_+)
$$

the Hamiltonian becomes:

$$
H = -\frac{\omega_q}{2}\sigma_z + \Omega V_d(t)\sigma_y
$$

where:

$$
\Omega = \frac{C_d}{C_\Sigma}Q_{\text{zpf}}
$$

This shows that the drive line naturally produces rotations about the $y$ axis.

---

## 6. Moving to the Rotating Frame

The Hamiltonian

$$
H = -\frac{\omega_q}{2}\sigma_z + \Omega V_d(t)\sigma_y
$$

is written in the laboratory frame, where the qubit precesses rapidly around the $z$ axis.

To simplify control, we apply the unitary transformation:

$$
U(t) = e^{i\omega_q t \sigma_z / 2}
$$

In the rotating frame:

$$
U(t)\sigma_y U^\dagger(t) = \cos(\omega_q t)\sigma_y - \sin(\omega_q t)\sigma_x
$$

This means that a $y$-drive in the lab frame becomes a combination of $x$ and $y$ rotations in the rotating frame.

---

## 7. IQ Modulation and the Rotating Wave Approximation

A realistic drive signal is:

$$
V_d(t) = V_0 s(t)\sin(\omega_d t + \phi)
$$

Using trigonometric identities, this can be written as:

$$
V_d(t) = V_0 s(t)\left[I\sin(\omega_d t) - Q\cos(\omega_d t)\right]
$$

where:

$$
I = \cos\phi, \qquad Q = \sin\phi
$$

### Rotating Wave Approximation

After transforming to the rotating frame, terms appear at:

1. $\omega_d - \omega_q$
2. $\omega_d + \omega_q$

The high-frequency sum terms average to zero, so they are dropped. This is the Rotating Wave Approximation.

If the drive is on resonance, $\delta\omega = \omega_q - \omega_d = 0$, then:

$$
H_d' = -\frac{\Omega}{2}V_0 s(t)\left(I\sigma_x + Q\sigma_y\right)
$$

### X and Y Gates

- $X$ gate: set $\phi = 0$, so $I=1$, $Q=0$
- $Y$ gate: set $\phi = \pi/2$, so $I=0$, $Q=1$

### Rotation Angle

The total rotation angle is:

$$
\Theta(t) = -\Omega V_0 \int_0^t s(t')\,dt'
$$

A perfect $\pi$-pulse is obtained by calibrating the pulse duration or amplitude so that the pulse area equals $\pi$.

---

## 8. Hardware: I/Q Modulation

The drive signal is produced using I/Q modulation:

$$
V_d(t) = V_0 s(t)\left[I\sin(\omega_d t) - Q\cos(\omega_d t)\right]
$$

### Components

1. **Local Oscillator (LO)**: generates a high-frequency microwave tone
2. **Arbitrary Waveform Generator (AWG)**: outputs low-frequency envelopes $I(t)$ and $Q(t)$
3. **IQ Mixer**: combines the LO with the AWG outputs to create the final microwave drive

The resulting drive frequency is:

$$
\omega_d = \omega_{\text{LO}} + \omega_{\text{AWG}}
$$

### Frequency Multiplexing

Multiple qubits or resonators can be addressed by sending several intermediate frequencies through the same physical line.

---

## 9. What Is a Virtual Z-Gate?

A physical $Z$ gate rotates the qubit state around the $z$ axis, changing its relative phase.

Traditionally, this was done using a flux pulse. However, flux pulses add noise, calibration errors, and distortion.

A **virtual Z-gate** performs the same phase update with no physical pulse. Instead, the software updates the reference phase of future control pulses.

---

## 10. Mathematics of Virtual Z-Gates

From the rotating-frame drive Hamiltonian:

$$
H_d' = -\frac{\Omega}{2}V_0 s(t)\left(I\sigma_x + Q\sigma_y\right)
$$

a virtual $Z_\phi$ gate rotates the control coordinates:

$$
I' = I\cos\phi - Q\sin\phi
$$

$$
Q' = I\sin\phi + Q\cos\phi
$$

### Example: Turning an X-Gate into a Y-Gate

Suppose an $X$-gate is followed by a virtual $Z$-gate of $\phi = \pi/2$.

Before the virtual gate:

- $I=1$
- $Q=0$

After the transformation:

$$
I' = (1)\cos\left(\frac{\pi}{2}\right) - (0)\sin\left(\frac{\pi}{2}\right) = 0
$$

$$
Q' = (1)\sin\left(\frac{\pi}{2}\right) + (0)\cos\left(\frac{\pi}{2}\right) = 1
$$

So the pulse is effectively shifted from the $I$ channel to the $Q$ channel.

### Why Virtual Z-Gates Are Useful

| Feature | Physical Z-Gate | Virtual Z-Gate |
|---|---|---|
| Execution time | Finite | Instantaneous |
| Fidelity | Limited by flux noise | Effectively perfect |
| Hardware required | Fast flux line | Software phase update |
| Decoherence during gate | Yes | No |

Virtual $Z$-gates are standard in modern superconducting quantum processors.