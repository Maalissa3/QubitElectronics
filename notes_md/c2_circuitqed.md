# Circuit QED

---

## 1. From Atomic Physics to Quantum Optics

Quantum optics historically began with atomic physics, studying how isolated atoms interact with freely propagating or weakly confined electromagnetic fields.

### Two-Level System Driven by a Field

A physical quantum system with a complex Hilbert space can often be truncated to a **two-level system (TLS)** when the driving field is near-resonant with a specific transition. This interaction between light and matter is dominated by the electric dipole coupling, where the atom's internal charge distribution shifts in response to an external electric field:

$$H_{\text{int}} = -\vec{d} \cdot \vec{E}(t)$$

Where $\vec{d} = q\vec{r}$ is the electric dipole moment operator. When driven by a resonant classical microwave or optical field:

$$\vec{E}(t) = \vec{E}_0 \cos(\omega_{01} t)$$

The field oscillates exactly at the transition frequency $\omega_{01} = \frac{E_1 - E_0}{\hbar}$ between the ground state $|0\rangle$ and excited state $|1\rangle$. This classical drive induces coherent **Rabi oscillations**, swapping the population between states at a frequency $\Omega_R \propto \vec{d} \cdot \vec{E}_0$.

### Single-Photon Coupling and Vacuum Fluctuations

In free space, the interaction between a single photon and an atom is incredibly weak because the photon's energy is spread over a large spatial volume. The fundamental question of cavity quantum electrodynamics (QED) is: *Can a single photon, or even pure vacuum fluctuations (zero-point energy), produce a measurable effect on an atom?*

The answer becomes yes when the electromagnetic field is strongly confined within a small volume $V$. The root-mean-square vacuum electric field fluctuation $E_{\text{zpf}}$ inside a volume $V$ is given by:

$$E_{\text{zpf}} = \sqrt{\frac{\hbar \omega}{2 \epsilon_0 V}}$$

As the cavity volume $V$ decreases, $E_{\text{zpf}}$ increases. This boosts the light-matter coupling strength $g = \vec{d} \cdot \vec{E}_{\text{zpf}}$, allowing a single photon to alter the quantum state of the atom before it decays.

---

## 2. Cavity QED Foundations

### The Fabry–Pérot Resonator

In atomic cavity QED, light confinement is achieved using a **Fabry–Pérot cavity**, which consists of two highly reflective parallel mirrors separated by a macroscopic distance $L$.

Boundary conditions enforce that the electric field must vanish at the mirror surfaces. Consequently, only specific wavelengths can survive inside the cavity due to constructive interference. This yields the strict resonance condition:

$$2L = m\lambda \implies f_m = \frac{m c}{2L}$$

Where $m$ is an integer mode number, $\lambda$ is the wavelength, and $c$ is the speed of light in the medium.

### Quantization of the Electromagnetic Field

The cavity quantizes the continuous spectrum of free-space light into discrete, isolated resonant modes. Each mode $m$ behaves mathematically as an independent quantum harmonic oscillator characterized by:

* Its resonance frequency $\omega_m = 2\pi f_m$.
* Its photon loss linewidth $\kappa_m$, which defines how quickly photons leak out through the imperfect mirrors.

The mode spacing (Free Spectral Range, $\text{FSR} = \frac{c}{2L}$) must be significantly larger than the linewidth $\kappa$ to ensure the modes remain isolated and do not overlap.

---

## 3. From Cavity QED to Circuit QED

### Why Shift to Circuit QED?

While atomic cavity QED offers an excellent platform for fundamental physics, it faces scalability and coupling limitations. **Circuit QED (cQED)** maps these exact same quantum optical principles onto a solid-state superconducting chip, swapping physical atoms for superconducting circuits and mirrors for transmission lines.

```
       ATOMIC CAVITY QED                       CIRCUIT QED (cQED)
+-------------------------------+       +-------------------------------+
|  • Alkali Atom                |       |  • Transmon Qubit             |
|  • Fabry-Pérot Mirrors        | ----> |  • Coplanar Waveguide (CPW)   |
|  • Optical Photons            |       |  • Microwave Photons          |
|  • Small Dipole Moment (ea0)  |       |  • Large Dipole Moment (~10⁴) |
+-------------------------------+       +-------------------------------+

```

### Key Advantages of Circuit QED

* **Massive Artificial Dipole Moments:** Natural atoms have small dipole moments bounded by the Bohr radius ($d \sim e a_0$). Superconducting qubits are macroscopic circuits where Cooper pairs tunnel across micrometric junctions. This gives them artificial dipole moments that are $10^4$ to $10^5$ times larger than natural atoms.
* **Ultra-Tight Field Confinement:** Instead of a 3D optical cavity, cQED uses 1D coplanar waveguide (CPW) micro-resonators. The cross-sectional mode area drops from square micrometers to square nanometers, confining the electric field and maximizing $E_{\text{zpf}}$.
* **Deep Strong Coupling ($g \gg \kappa, \gamma$):** Due to the massive dipole moment and small mode volume, the coupling rate $g$ easily outpaces the qubit decay rate $\gamma$ and cavity loss rate $\kappa$. This places the system deep within the **strong coupling regime**.
* **Engineered Architecture:** Features like transition frequencies, coupling constants, and decay rates are completely customizable using standard lithographic cleanroom processing.

### Typical Implementation Layout

* **The Cavity:** A superconducting coplanar transmission-line resonator (often made of Niobium or Aluminum) acting as a microwave cavity.
* **The Artificial Atom:** A transmon qubit placed at an electric field anti-node (maximum) inside the resonator.

This integrated layout allows for coherent control of the qubit using microwave pulses, as well as high-fidelity state readout by monitoring how the qubit alters the resonator's properties.

---

## 4. Modeling the Resonator as a Single Mode

### One-Mode Approximation

A physical coplanar waveguide resonator acts as a distributed-element transmission line with an infinite series of harmonic modes ($\omega_r, 2\omega_r, 3\omega_r, \dots$). However, if the mode spacing is much larger than the linewidth of each mode:

$$\kappa_m \ll |\omega_{m+1} - \omega_m|$$

And if the system's drive frequencies are restricted near the fundamental frequency $\omega_r$, we can drop the higher-order modes. This reduces the complex transmission line to a single-mode approximation.

### Lumped-Element $LC$ Analogue

Under this approximation, the fundamental mode of the resonator maps directly to an equivalent lumped-element parallel $LC$ resonant circuit:

$$\omega_r = \frac{1}{\sqrt{L_r C_r}}$$

Where $L_r$ and $C_r$ are the equivalent total inductance and capacitance of the fundamental mode. The loss rate $\kappa$ (the full-width at half-maximum of the resonator's spectral line) is determined by its internal and external Quality Factor $Q$:

$$\kappa = \frac{\omega_r}{Q}$$

### Zero-Point Fluctuations

Quantizing the $LC$ circuit reveals that the charge $\hat{Q}$ and flux $\hat{\Phi}$ operators act as canonical conjugate variables ($[\hat{\Phi}, \hat{Q}] = j\hbar$). Even in its absolute ground state $|0\rangle$, the resonator maintains zero-point energy, which translates to a fluctuating vacuum voltage across the capacitor:

$$V_{\text{zpf}} = \omega_r \Phi_{\text{zpf}} = \sqrt{\frac{\hbar \omega_r}{2 C_r}}$$

In typical superconducting chips, this zero-point voltage fluctuation is approximately $V_{\text{zpf}} \sim 1\ \mu\text{V}$. While this value sounds small, when applied across a sub-micron gate gap, it creates a local electric field strong enough to drive artificial atoms.

---

## 5. Total System Hamiltonian

The complete Hamiltonian of a circuit QED system combines the energy of the isolated resonator, the isolated transmon qubit, and the capacitive interaction zone between them:

$$H = H_{\text{r}} + H_{\text{q}} + H_{\text{int}}$$

### Resonator Hamiltonian ($H_{\text{r}}$)

Expressed using its continuous conjugate lumped variable operators (Charge $\hat{Q}_r$ and Flux $\hat{\Phi}_r$):

$$H_{\text{r}} = \frac{\hat{Q}_r^2}{2C_r} + \frac{\hat{\Phi}_r^2}{2L_r}$$

By introducing the standard boson creation ($\hat{a}^\dagger$) and annihilation ($\hat{a}$) operators, where $\hat{a}^\dagger \hat{a} = \hat{n}$ represents the photon number operator, the quantized harmonic oscillator simplifies to:

$$H_{\text{r}} = \hbar \omega_r \left( \hat{a}^\dagger \hat{a} + \frac{1}{2} \right)$$

### Transmon Qubit Hamiltonian ($H_{\text{q}}$)

A transmon is a non-linear Josephson oscillator defined by its charging energy $E_c$ and Josephson tunneling energy $E_J$:

$$H_{\text{q}} = 4E_c \hat{n}^2 - E_J \cos \hat{\varphi}$$

Where $\hat{n}$ is the Cooper-pair number operator and $\hat{\varphi}$ is the gauge-invariant phase operator across the junction. The non-linear $\cos\hat{\varphi}$ potential introduces **anharmonicity**, shifting the higher energy levels so the $0 \to 1$ transition can be isolated from the $1 \to 2$ transition.

### Total Un-Truncated Expression

Combining these terms yields the total combined system Hamiltonian before any two-level truncation:

$$H = \left( \frac{\hat{Q}_r^2}{2C_r} + \frac{\hat{\Phi}_r^2}{2L_r} \right) + \left( 4E_c \hat{n}^2 - E_J \cos \hat{\varphi} \right) + H_{\text{int}}$$

---

## 6. Two-Level System Approximation

### Qubit Truncation

Because the transmon's anharmonicity isolates the ground state $|0\rangle$ and first excited state $|1\rangle$, we can truncate the infinite-dimensional transmon Hilbert space down to a simple two-level system. Using the standard Pauli spin operators ($\sigma_x, \sigma_y, \sigma_z$), the isolated qubit Hamiltonian simplifies to:

$$H_{\text{q}} \approx \frac{\hbar \omega_{01}}{2} \sigma_z$$

Where $\sigma_z = |1\rangle\langle1| - |0\rangle\langle0|$.

### The Interaction Term ($H_{\text{int}}$)

The physical link between the transmon and the resonator is a coupling capacitor $C_g$. The interaction energy depends on the voltage across the resonator and the charge on the qubit:

$$H_{\text{int}} = - \hat{q}_{\text{qubit}} \cdot \hat{V}_{\text{resonator}}$$

Expressing the resonator voltage via creation/annihilation operators yields $\hat{V}_r = V_{\text{zpf}}(\hat{a}^\dagger + \hat{a})$. Similarly, the qubit's charge operator maps to the atomic raising and lowering operators ($\sigma_+, \sigma_-$). This gives the interaction term its standard form:

$$H_{\text{int}} = \hbar g (\hat{a}^\dagger + \hat{a})(\sigma_- + \sigma_+)$$

Where $g$ represents the fundamental vacuum Rabi coupling strength. Expanding this product yields four distinct interaction terms:

$$H_{\text{int}} = \hbar g \left( \hat{a}^\dagger \sigma_- + \hat{a} \sigma_+ + \hat{a}^\dagger \sigma_+ + \hat{a} \sigma_- \right)$$

---

## 7. The Jaynes–Cummings Model

### The Rotating Wave Approximation (RWA)

To simplify the interaction expression, we transform the system into an uncoupled rotating interaction frame. In this frame, the terms oscillate at different rates:

* **Coherent Exchange Terms ($\hat{a} \sigma_+$ and $\hat{a}^\dagger \sigma_-$):** These terms oscillate at the difference frequency $(\omega_{01} - \omega_r)$. Near resonance, this difference is small, meaning these terms oscillate slowly and exert a measurable net force on the system.
* **Counter-Rotating Terms ($\hat{a} \sigma_-$ and $\hat{a}^\dagger \sigma_+$):** These terms oscillate rapidly at the sum frequency $(\omega_{01} + \omega_r)$. Over measurable timescales, these rapid oscillations average out to zero.

Rejection of these fast counter-rotating terms is called the **Rotating Wave Approximation (RWA)**. Dropping them yields the iconic **Jaynes–Cummings Hamiltonian**:

$$H_{\text{JC}} = \hbar \omega_r \hat{a}^\dagger \hat{a} + \frac{\hbar \omega_{01}}{2} \sigma_z + \hbar g \left( \hat{a}^\dagger \sigma_- + \hat{a} \sigma_+ \right)$$

### Physical Mechanics

The surviving interaction terms describe the coherent, bi-directional exchange of energy between the artificial atom and the confined field:

* $\hbar g \hat{a}^\dagger \sigma_-$: The qubit drops from the excited state to the ground state ($|1\rangle \to |0\rangle$) and emits a single microwave photon ($\hat{a}^\dagger$) into the resonator.
* $\hbar g \hat{a} \sigma_+$: The qubit absorbs a single microwave photon ($\hat{a}$) from the resonator and transitions from the ground state to the excited state ($|0\rangle \to |1\rangle$).

This coherent energy exchange occurs at the **Vacuum Rabi Frequency** $2g$. The Jaynes-Cummings model provides the standard description for both cavity and circuit QED within the weak-coupling, near-resonant regime ($\Delta \to 0$).

---

## 8. The Dispersive Regime

### Detuning Condition

When the qubit's transition frequency and the resonator's base frequency are intentionally detuned far apart, the energy difference is defined as:

$$\Delta = \omega_{01} - \omega_r$$

The system enters the **dispersive regime** when this detuning significantly exceeds the coupling and loss rates:

$$|\Delta| \gg g, \kappa, \gamma$$

### Consequence: Dressed Frequencies

Because $|\Delta| \gg g$, there is not enough energy to directly swap excitations between the qubit and the resonator. Real photon absorption and emission are suppressed. Instead, virtual photon exchanges induce a **state-dependent frequency shift**.

Applying a second-order perturbation (Schrieffer–Wolff transformation) to the Jaynes-Cummings Hamiltonian drops the direct exchange terms and yields the effective Dispersive Hamiltonian:

$$H_{\text{disp}} \approx \hbar \left( \omega_r + \chi \sigma_z \right) \hat{a}^\dagger \hat{a} + \frac{\hbar \omega_{01}'}{2} \sigma_z$$

Where $\chi = \frac{g^2}{\Delta}$ is the **dispersive shift coefficient**, and $\omega_{01}' = \omega_{01} + \chi$ represents the Lamb-shifted qubit frequency.

### Physical Interpretation

Grouping the terms highlights how the resonator's effective frequency depends directly on the state of the qubit:

$$H_{\text{disp}} = \hbar \underbrace{\left( \omega_r + \chi \sigma_z \right)}_{\omega_{r,\text{eff}}} \hat{a}^\dagger \hat{a} + \frac{\hbar \omega_{01}'}{2} \sigma_z$$

$$\omega_{r,\text{eff}} = \begin{cases} \omega_r + \chi, & \text{if qubit is in } |1\rangle \, (\sigma_z = +1) \\ \omega_r - \chi, & \text{if qubit is in } |0\rangle \, (\sigma_z = -1) \end{cases}$$

This state-dependent frequency splitting forms the foundation of non-destructive **dispersive readout**.

---

## 9. Dispersive Qubit Readout

### The Readout Principle

To measure the qubit, a weak microwave tone is sent into the resonator at its bare resonance frequency $\omega_r$.

Because the resonator's actual resonance frequency shifts down to $\omega_r - \chi$ (if $|0\rangle$) or up to $\omega_r + \chi$ (if $|1\rangle$), the transmission curve shifts across the frequency axis. As a result, the reflected or transmitted microwave signal experiences a phase shift $\Delta \theta \approx \pm 2\arctan(2\chi/\kappa)$ depending on the qubit state.

This phase shift is measured using room-temperature electronics (such as the I/Q demodulation framework discussed previously) to determine the qubit state.

### High-Fidelity Readout Parameters

To achieve high-fidelity readout, the state-dependent frequency shift must be larger than the cavity's linewidth ($\chi > \kappa$), allowing the two response curves to be clearly distinguished. However, $\kappa$ cannot be too small, or photons will take too long to exit the cavity, slowing down the readout process.

### Operational Parameters Table

| Parameter Metric | 3D Waveguide Cavity Layout | 2D Coplanar Waveguide Layout |
| --- | --- | --- |
| **Coupling Strength ($g/2\pi$)** | $50 - 150\ \text{MHz}$ | $100 - 300\ \text{MHz}$ |
| **Cavity Decay Rate ($\kappa/2\pi$)** | $1 - 10\ \text{kHz}$ (High-$Q$ storage) | $5 - 10\ \text{MHz}$ (Fast Readout) |
| **Qubit Relaxation Time ($T_1$)** | Up to $100 - 200\ \mu\text{s}$ | $50 - 150\ \mu\text{s}$ |
| **Readout Mechanism Type** | Dispersive (Nondestructive) | Dispersive (Nondestructive) |

### Control vs. Readout Separation

This framework completely separates qubit control from qubit readout:

* **Qubit Control Line:** Driven near $\omega_{01}$ to induce Rabi rotations and execute single-qubit gates.
* **Qubit Readout Line:** Driven near $\omega_r$ to measure the qubit's state via the resonator's phase shift.

This spectral separation allows independent optimization of control and measurement paths.

---

## 10. Towards Xmon Qubits

### Evolution of the Transmon

The standard transmon qubit consists of a Cooper-pair box shunted by a large capacitor to reduce its sensitivity to charge noise. Early versions used interdigital finger capacitors, which had limited coupling options and larger electric field footprints.

### The Xmon Architecture

The **Xmon** variant rearranges this shunted capacitance into a cross-shaped geometry.

This cross-shaped design provides distinct architectural advantages:

* **Multi-Port Connectivity:** The four arms of the cross provide independent connectivity points. One arm couples to a readout resonator, another to a drive line, a third to an adjacent qubit for multi-qubit gates, and the fourth can be used for a dedicated flux-tuning line.
* **Scalable Footprint:** The orthogonal layout simplifies the design of 2D planar grids for large-scale quantum processors.

### Magnetic Flux Tunability

By replacing the single Josephson junction with a parallel pair forming a **SQUID loop (Superconducting Quantum Interference Device)**, the qubit's transition frequency can be tuned using an external magnetic flux $\Phi_{\text{ext}}$.

The external flux alters the effective Josephson energy $E_J$ according to the relation:

$$E_J(\Phi_{\text{ext}}) = 2E_{J0} \left| \cos\left( \frac{\pi \Phi_{\text{ext}}}{\Phi_0} \right) \right|$$

Where $\Phi_0 = \frac{h}{2e}$ is the magnetic flux quantum. Because the qubit frequency scales as $\omega_{01} \approx \sqrt{8E_c E_J}$, changing the local magnetic flux shifts the qubit's frequency. This allows processors to quickly tune qubits into resonance for two-qubit gates or detune them to prevent unwanted cross-talk.

### Compact Resonator Layouts

To package multiple qubits onto a single chip, straight coplanar waveguide resonators are wound into compact **meander line shapes**. These meanders reduce the physical area of each readout channel, enabling higher packing densities on superconducting quantum processing units (QPUs).

---

## 11. Core Mathematical Reference Sheet

$$\begin{array}{ll}
\hline
\textbf{Quantum Metric / Model} & \textbf{Mathematical Formulation} \\ \hline
\text{Electric Dipole Interaction} & H_{\text{int}} = -\vec{d} \cdot \vec{E}(t) \\
\text{Vacuum Field Fluctuation} & E_{\text{zpf}} = \sqrt{\frac{\hbar \omega}{2 \epsilon_0 V}} \\
\text{Lumped Resonator Frequency} & \omega_r = \frac{1}{\sqrt{L_r C_r}} \\
\text{Jaynes-Cummings Expression} & H_{\text{JC}} = \hbar \omega_r \hat{a}^\dagger \hat{a} + \frac{\hbar \omega_{01}}{2} \sigma_z + \hbar g \left( \hat{a}^\dagger \sigma_- + \hat{a} \sigma_+ \right) \\
\text{Dispersive Regime Shift} & H_{\text{disp}} \approx \hbar \left( \omega_r + \chi \sigma_z \right) \hat{a}^\dagger \hat{a} + \frac{\hbar \omega_{01}'}{2} \sigma_z \quad \left(\text{where } \chi = \frac{g^2}{\Delta}\right) \\
\text{Flux-Tuned Josephson Energy} & E_J(\Phi_{\text{ext}}) = 2E_{J0} \left| \cos\left( \pi \Phi_{\text{ext}} / \Phi_0 \right) \right| \\ \hline
\end{array}$$