# Qubit Characterization


## 1. Closed vs. Open Quantum Systems

The fundamental challenge in quantum computing is moving from an ideal theoretical system to a real physical system.

- **Closed systems (ideal):** the qubit is perfectly isolated. Its evolution is deterministic and fully described by its Hamiltonian.
- **Open systems (realistic):** the qubit interacts with its environment. These uncontrolled interactions introduce noise and lead to loss of quantum information.

---

## 2. Types of Noise: Systematic vs. Stochastic

Environmental noise is typically divided into two categories.

| Noise Type | Characteristics | Example | Solution |
|---|---|---|---|
| Systematic noise | Non-random, reproducible errors caused by fixed control or readout flaws | A microwave pulse consistently over-rotates a qubit because of poor calibration | Calibration or hardware improvement |
| Stochastic noise | Random, unpredictable fluctuations from parameters coupled to the qubit | Thermal noise, charge noise, flux noise, fluctuating magnetic or electric fields | Hard to eliminate; causes decoherence |

---

## 3. The Bloch Sphere Representation

The Bloch sphere is a geometric tool used to visualize the state of a two-level quantum system.

### Geometric breakdown

- **Pure states:** the qubit is fully known and lies on the surface of the sphere, with $|\vec{a}| = 1$
- **Z-axis:** connects the north pole $|0\rangle$ and south pole $|1\rangle$
- **X-Y plane:** represents superposition states

A general qubit state can be written as:

$$
|\psi\rangle = \alpha|0\rangle + \beta|1\rangle
$$

and mapped to spherical coordinates as:

$$
\begin{pmatrix}
\sin\theta \cos\phi \\
\sin\theta \sin\phi \\
\cos\theta
\end{pmatrix}
$$

with:

$$
|\psi\rangle = \cos\left(\frac{\theta}{2}\right)|0\rangle + e^{i\phi}\sin\left(\frac{\theta}{2}\right)|1\rangle
$$

### Reference frames

- **Laboratory frame:** the physical frame. A superposition state precesses around the $z$-axis at frequency $\omega_{01}$
- **Rotating frame:** rotates at the qubit frequency $\omega_{01}$, making the Bloch vector appear stationary

---

## 4. The Density Matrix and Real-World States

In noisy systems, a state vector $|\psi\rangle$ is not always enough. We use the **density matrix** $\hat{\rho}$.

- **Pure state:**  
  $$
  \hat{\rho} = |\psi\rangle\langle\psi|
  $$
  and
  $$
  \mathrm{Tr}(\hat{\rho}^2) = 1
  $$

- **Mixed state:** stochastic noise forces the qubit into a statistical mixture of states. The Bloch vector has length $|\vec{a}| < 1$ and lies inside the Bloch sphere. In both pure and mixed cases, the density matrix is normalized so that $\mathrm{Tr}(\hat{\rho}) = 1$.

A density matrix can be written in terms of the Bloch vector $\vec{a}$ as:

$$
\hat{\rho} = \frac{1}{2}\left(I + \vec{a}\cdot\vec{\sigma}\right)
$$

This compact form describes the qubit state, but it does not by itself include the action of environmental noise.

To account for weak coupling to a Markovian bath, one can use open-system models such as the **Bloch-Redfield** formalism, which adds dissipative terms to the density-matrix evolution.

---

## 5. Decoherence

Decoherence is the process by which a qubit loses its quantum properties due to interaction with the environment.

In superconducting qubits such as transmons, decoherence is usually split into:

1. **Longitudinal relaxation** $(T_1)$: loss of energy  
2. **Pure dephasing** $(T_\phi)$: loss of phase information

Together, these determine the transverse relaxation time $T_2$.

---

## 6. Longitudinal Relaxation ($T_1$)

Also called energy relaxation or depolarization, this is the process in which the qubit decays from $|1\rangle$ to $|0\rangle$.

### Physics

- Caused by transverse noise, i.e. fluctuations along $x$ or $y$
- This noise couples to the qubit transition frequency $\omega_q$

The total relaxation rate is:

$$
\Gamma_1 \equiv \frac{1}{T_1} = \Gamma_{1\downarrow} + \Gamma_{1\uparrow}
$$

Because superconducting qubits operate in very cold dilution refrigerators, the thermal excitation rate is negligible:

$$
\Gamma_{1\uparrow} = \Gamma_{1\downarrow}e^{-\frac{\hbar\omega_q}{k_BT}} \approx 3\times 10^{-7}\Gamma_{1\downarrow}
$$

So $T_1$ is dominated by downward decay.

### How $T_1$ is measured

1. Apply an $X_\pi$ pulse to prepare the qubit in $|1\rangle$
2. Wait for a variable delay time $\tau$
3. Measure the qubit along the $z$ axis
4. Repeat many times and fit the decay

The excited-state population follows:

$$
P_{|1\rangle}(\tau) \propto e^{-\tau/T_1}
$$

At $\tau = T_1$, the population has decayed to about $36.8\%$ of its initial value.

---

## 7. Pure Dephasing ($T_\phi$)

Pure dephasing is the loss of relative phase in a superposition state, without energy loss.

### Physics

- Associated with noise that causes the qubit frequency to fluctuate, inducing precession along the $z$-axis of the state vector
- Caused by **longitudinal noise**, i.e. fluctuations along the $z$ axis, which add a time-dependent shift $\delta\omega(t)$ to the qubit frequency
- On resonance, in the rotating frame, the ideal $H'$ is zero; longitudinal noise makes the Bloch vector precess forward or backward in the rotating frame depending on $\delta\omega(t)$
- It is represented by the rate $\Gamma_\phi$, which describes depolarization in the $x$-$y$ plane of the Bloch sphere

In the rotating frame, the Bloch vector wanders around the equator. When averaged over many repetitions, the phase randomization shrinks the vector toward the center of the sphere.

- Dephasing is a stochastic effect, and the transverse relaxation parameter captures it
- Ensemble averaging over random phase shifts makes the average state vector shorter than 1 in length
- This averaging causes decay of transverse coherence
- Unlike energy relaxation, pure dephasing is not a resonant phenomenon; noise at any frequency can modify the qubit frequency and cause dephasing

---

## 8. Transverse Relaxation ($T_2$)

The total decay of superposition coherence is given by:

$$
\Gamma_2 = \frac{1}{T_2^*} = \frac{\Gamma_1}{2} + \Gamma_\phi = \frac{1}{2T_1} + \frac{1}{T_\phi}
$$

### Why $\Gamma_1/2$ appears

Energy relaxation is also a phase-breaking process. Once the qubit falls to $|0\rangle$, any phase information on the equator is lost. That is why $1/(2T_1)$ contributes to the total decoherence rate.

---

## 9. Measuring $T_2$: Ramsey vs. Spin Echo

Two main protocols are used to measure coherence.

### Protocol A: Ramsey Measurement

1. Apply an $X_{\pi/2}$ pulse to place the qubit on the equator
2. Wait for a time $\tau$ with a small detuning $\delta\omega$
3. Apply a second $X_{\pi/2}$ pulse
4. Read out the qubit

The result is Ramsey fringes, which decay with a characteristic time $T_2^*$.

This method is very sensitive to slow, low-frequency noise.

### Protocol B: Spin Echo

This is the same as Ramsey, but an $X_\pi$ pulse is inserted halfway through the waiting time.

- The $\pi$ pulse reverses the phase accumulation
- Slow noise that acted during the first half is refocused in the second half

As a result, the coherence time measured with spin echo is typically larger than $T_2^*$.

---

## 10. Takeaways for Transmon Qubits

- For transmon qubits, pure dephasing $T_\phi$ is often relatively small
- In practice, coherence is usually limited mainly by energy relaxation $T_1$
- A central goal of quantum engineering is to maximize both $T_1$ and $T_2$ so that quantum circuits can run before information decays

---

## 11. Example Measurements

These experiments are commonly used in the qubit characterization workflow.

### Time of Flight (TOF)

- First calibration step in the characterization chain
- Used to determine the propagation delay between readout pulse generation and the arrival of the corresponding signal at the acquisition input in the RFSoC
- Applied to all subsequent measurements

### Resonator Spectroscopy

- Used to identify the resonator’s fundamental frequency
- Obtained by sweeping the readout tone across one or more frequency ranges and measuring the transmitted signal amplitude
- In this experiment the qubit is decoupled from the resonator, so high readout power is used
- No drive pulse is generated, therefore the qubit is in principle completely unaffected
- Sweeps are typically started over a broad frequency range and then narrowed around the peak

### Resonator Punchout

- Used to identify the optimal readout configuration in the dispersive regime
- Consists of a cartesian sweep over readout gain and frequency, providing a map of the device power response
- At high readout power the Josephson junction nonlinearity is not observed, revealing the bare resonance of the resonator
- As the power is reduced, the coupling becomes significant and the resonance shifts toward the dispersive readout frequency
- The dark absorption region at low power corresponds to the dressed resonance

### Drive Spectroscopy

- Also called qubit spectroscopy
- A two-tone experiment: a drive pulse is sent through the qubit control line and is immediately followed by the readout tone
- The goal of the drive frequency sweep is to calibrate the drive pulse to the qubit transition frequency
- A high number of shots is required because the signal-to-noise ratio is very low

### Rabi Oscillations

- Used to identify the drive amplitude required to calibrate the $\pi$ pulse
- Three fundamental parameters must be tuned: drive frequency, drive duration, and drive gain
- The experiment can be implemented as a drive-gain sweep or a drive-duration sweep
- The sweeping pulse is modulated at the previously identified transition frequency and is sent to the qubit to produce a full excitation
- For finer characterization, a two-dimensional cartesian sweep over both drive duration and gain can be used
- Once the $\pi$ pulse is calibrated, the $\pi/2$ pulse is obtained by halving the pulse energy

---
