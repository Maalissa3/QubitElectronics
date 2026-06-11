# Superconducting Qubits

**Fabrizio Riente** *fabrizio.riente@polito.it* Politecnico di Torino – QNANO Group

---

## 1. Superconductivity Review

Superconductivity is a macroscopic quantum phenomenon where electrons form a collective state that flows without resistance.

### Cooper Pairs

In 1957, Leon Cooper, John Bardeen, and Robert Schrieffer proposed the BCS theory, establishing that superconductivity arises from the formation of bound electron pairs called **Cooper pairs**.

At low temperatures, an electron traveling through a crystal lattice slightly distorts the surrounding positively charged ions. This local polarization creates an attractive potential that draws in a second electron. The resulting bound Cooper pair consists of two strongly correlated electrons with:

* Opposite momentum ($\vec{p}_1 = -\vec{p}_2$)
* Opposite spin ($\uparrow\downarrow$), forming a spin-singlet state with an overall spin $S=0$

Because they have integer spin, Cooper pairs act as bosons. Below a material's **critical temperature ($T_c$)**, they undergo a process akin to Bose-Einstein condensation, collapsing into a single, coherent macroscopic quantum state described by a unified wave function.

### The Macroscopic Wave Function

The coherent quantum condensate can be expressed as a complex scalar order parameter:

$$\psi(\vec{x}, t) = \psi_0 e^{-j\frac{1}{\hbar}(Et - \vec{p}\cdot\vec{x})} = \sqrt{\rho} e^{j\theta(\vec{x}, t)}$$

Where:

* $\rho = \psi\psi^* = |\psi_0|^2$ is the physical local density of Cooper pairs.
* $\theta(\vec{x}, t)$ is the macroscopic quantum phase.

### Key Electromagnetic Properties

1. **Zero Electrical Resistance:** Because the Cooper pairs are locked into a single macroscopic state, scattering from individual lattice defects is energetically suppressed, dropping DC electrical resistance to zero.
2. **Phase Coherence:** The phase $\theta$ is rigid across the entire superconductor, locking the material into a coherent quantum system.
3. **Critical Magnetic Field ($H_c$):** Applying an external magnetic field raises the free energy of the superconducting state. If the field exceeds the critical magnetic field $H_c$, superconductivity breaks down, and the material reverts to its normal resistive state. $H_c$ follows a parabolic temperature dependence:
$$H_c(T) = H_c(0) \left[1 - \left(\frac{T}{T_c}\right)^2\right]$$



### The Meissner Effect

Discovered in 1933 by Walter Meissner and Robert Ochsenfeld, the Meissner effect shows that a superconductor does more than act as an ideal conductor. When cooled below $T_c$ in the presence of a magnetic field, it actively expels all internal magnetic flux lines from its bulk.

The material sets up persistent surface screening currents that generate an internal field that cancels out the external field, causing it to behave as a perfect diamagnet with a magnetic susceptibility $\chi = -1$.

---

## 2. The Josephson Junction

### Physical Definition

In 1962, Brian D. Josephson analyzed a system consisting of two superconducting layers ($S_1, S_2$) separated by a thin insulating barrier ($I$). This configuration forms an **SIS Josephson Junction**.

```
    Superconductor 1 (S1)         Insulator (I)         Superconductor 2 (S2)
+---------------------------+   +---------------+   +---------------------------+
|  Wavefunction: ψ1         |   |   Thickness   |   |  Wavefunction: ψ2         |
|  Phase: θ1                |===|   0.1 - 1 nm  |===|  Phase: θ2                |
+---------------------------+   +---------------+   +---------------------------+
                                        |
                                        v
                          [Cooper Pair Tunneling Loop]

```

When the insulating barrier is thin enough ($0.1–1\ \text{nm}$), the macroscopic wave functions of the two superconductors overlap within the barrier, allowing Cooper pairs to tunnel coherently across the gap without breaking apart.

### Coupled Superconductors Derivation

Let the isolated states of the two superconductors be:

$$\psi_1 = \sqrt{\rho_1} e^{j\theta_1}, \quad \psi_2 = \sqrt{\rho_2} e^{j\theta_2}$$

The relative phase difference across the barrier is defined as:

$$\phi = \theta_2 - \theta_1$$

When the two systems are coupled across the thin barrier, their behavior is governed by a coupled set of Schrödinger equations:

$$j\hbar \frac{\partial \psi_1}{\partial t} = E_1 \psi_1 + K_S \psi_2$$

$$j\hbar \frac{\partial \psi_2}{\partial t} = E_2 \psi_2 + K_S \psi_1$$

Where $K_S$ is the coupling coefficient determined by the barrier thickness and geometry. Substituting the wave functions into these equations isolates the dynamics of the Cooper pair densities ($\rho_1, \rho_2$) and the relative phase ($\phi$).

---

## 3. The Constitutive Josephson Relations

Evaluating the coupled state equations yields the two fundamental Josephson relations that govern the behavior of the junction.

### 1. The Current-Phase Relation (First Josephson Relation)

The charge current $I$ passing through the junction is directly proportional to the sine of the relative macroscopic phase difference:

$$I = I_c \sin \phi$$

Where $I_c$ is the **critical current** of the junction—the maximum supercurrent the barrier can support before a voltage drops across it.

### 2. The Voltage-Phase Relation (Second Josephson Relation)

When a voltage $V$ exists across the junction, the relative phase difference evolves linearly over time:

$$V = \frac{\Phi_0}{2\pi} \frac{d\phi}{dt} = \frac{\hbar}{2e} \frac{d\phi}{dt}$$

Where $\Phi_0$ is the **magnetic flux quantum**, a fundamental physical constant given by:

$$\Phi_0 = \frac{h}{2e} \approx 2.0678 \times 10^{-15}\ \text{Wb}$$

---

## 4. Josephson Inductance and Nonlinearity

The Josephson junction acts as a non-linear inductor. We can show this by differentiating the current-phase relation with respect to time:

$$\frac{dI}{dt} = I_c \cos \phi \left(\frac{d\phi}{dt}\right)$$

Substituting the voltage-phase relation ($\frac{d\phi}{dt} = \frac{2\pi V}{\Phi_0}$) into this equation yields:

$$\frac{dI}{dt} = I_c \cos \phi \left( \frac{2\pi V}{\Phi_0} \right)$$

Rearranging this into the standard form for an inductor ($V = L \frac{dI}{dt}$) isolates the **Josephson Inductance ($L_J$)**:

$$V = \left[ \frac{\Phi_0}{2\pi I_c \cos \phi} \right] \frac{dI}{dt} \implies L_J(\phi) = \frac{\Phi_0}{2\pi I_c \cos \phi}$$

By defining the characteristic constant inductance factor as $L_{J0} = \frac{\Phi_0}{2\pi I_c}$, the total parametric inductance becomes:

$$L_J(\phi) = \frac{L_{J0}}{\cos \phi}$$

### Physical Significance

Unlike a standard wire inductor where $L$ is a fixed value, the Josephson inductance depends nonlinearly on the phase variable $\phi$ (and therefore on the current passing through it). As $\phi \to \frac{\pi}{2}$, the inductance grows toward infinity. This clean, dissipationless nonlinearity is what enables the development of superconducting qubits.

---

## 5. Josephson Energy

The potential energy stored in the junction is calculated by integrating the electrical power over time:

$$E = \int I \cdot V \, dt$$

Substituting the two Josephson relations into this integral:

$$E = \int \left(I_c \sin \phi\right) \left(\frac{\Phi_0}{2\pi} \frac{d\phi}{dt}\right) dt = \frac{I_c \Phi_0}{2\pi} \int \sin \phi \, d\phi$$

Evaluating this integral yields the potential energy profile of the junction:

$$E(\phi) = E_J (1 - \cos \phi)$$

Where the characteristic **Josephson Energy ($E_J$)** scale is defined as:

$$E_J = \frac{I_c \Phi_0}{2\pi}$$

This cosine potential energy profile replaces the standard parabolic potential of a traditional linear inductor, introducing the anharmonicity needed to form an artificial atom.

---

## 6. Artificial Atoms vs. Natural Atoms

### The Energy Spectrum Problem

* **Natural Atoms:** Systems like Hydrogen or Rubidium have a naturally non-linear potential energy profile. This creates an unequally spaced energy spectrum, allowing an external laser to address a specific transition (e.g., $|0\rangle \to |1\rangle$) without driving higher-order transitions.
* **Standard $LC$ Circuits:** A standard lumped-element inductor connected to a capacitor forms a harmonic oscillator. Its potential energy profile is perfectly parabolic ($E \propto \Phi^2$), producing an equally spaced ladder of energy states ($E_n = \hbar\omega_0(n + 1/2)$).

[Image comparing a harmonic parabolic potential energy well with equally spaced levels to an anharmonic cosine potential well with unequally spaced levels]

If you attempt to use a standard $LC$ circuit as a qubit, an external microwave pulse meant to transition the system from $|0\rangle \to |1\rangle$ will also drive transitions from $|1\rangle \to |2\rangle$ and $|2\rangle \to |3\rangle$. This causes the quantum state to leak into higher energy levels, preventing the system from acting as a two-level qubit.

### Cryogenic Initialization

To initialize an artificial atom into its ground state $|0\rangle$, thermal energy must be significantly lower than the qubit's transition energy:

$$k_B T \ll \hbar \omega_{01}$$

For a typical qubit transition frequency $\omega_{01}/2\pi \approx 5\ \text{GHz}$, the equivalent thermal transition boundary is $T \approx 240\ \text{mK}$. Superconducting quantum processors are housed inside dilution refrigerators that reach baseline temperatures of $\sim 10\ \text{mK}$. At these temperatures, thermal fluctuations are suppressed, and the system cools reliably into its ground state.

---

## 7. Harmonic Oscillator Quantization

Before analyzing the non-linear case, we can review the canonical quantization of a standard linear $LC$ oscillator. The classical Hamiltonian of the circuit is expressed using charge $Q$ and flux $\Phi$:

$$H = \frac{Q^2}{2C} + \frac{\Phi^2}{2L}$$

Through canonical quantization, the continuous variables become non-commuting operators ($\hat{Q}, \hat{\Phi}$) that satisfy the commutation relation:

$$[\hat{\Phi}, \hat{Q}] = j\hbar$$

We define the standard creation ($\hat{a}^\dagger$) and annihilation ($\hat{a}$) operators to express the charge and flux operators in terms of the circuit's characteristic impedance $Z_0 = \sqrt{\frac{L}{C}}$:

$$\hat{\Phi} = \sqrt{\frac{\hbar Z_0}{2}}(\hat{a}^\dagger + \hat{a})$$

$$\hat{\Q} = j\sqrt{\frac{\hbar}{2 Z_0}}(\hat{a}^\dagger - \hat{a})$$

This transforms the linear Hamiltonian into its quantized form, featuring equally spaced energy rungs and a residual zero-point energy:

$$\hat{H} = \hbar \omega_r \left(\hat{a}^\dagger \hat{a} + \frac{1}{2}\right)$$

---

## 8. Superconducting Qubit Hamiltonian

By replacing the linear inductor with a non-linear Josephson junction, the parabolic potential energy term $\frac{\hat{\Phi}^2}{2L}$ is replaced by the junction's cosine potential energy profile:

$$\hat{H} = \frac{\hat{Q}^2}{2C} - E_J \cos\left(2\pi \frac{\hat{\Phi}}{\Phi_0}\right)$$

To simplify the math, we transition to dimensionless variables:

1. **The Reduced Phase Operator ($\hat{\phi}$):** 
$$\hat{\phi} = \frac{2\pi \hat{\Phi}}{\Phi_0}$$


2. **The Excess Cooper-Pair Number Operator ($\hat{n}$):** 
$$\hat{n} = \frac{\hat{Q}}{2e}$$



The canonical commutation relation for these dimensionless operators preserves the uncertainty principle:

$$[\hat{\phi}, \hat{n}] = j$$

Substituting these operators into the Hamiltonian yields the standard expression for a superconducting qubit:

$$\hat{H} = 4 E_c \hat{n}^2 - E_J \cos \hat{\phi}$$

Where $E_c$ represents the **Charging Energy** required to add a single Cooper pair to the circuit's capacitance:

$$E_c = \frac{e^2}{2C}$$

---

## 9. Charge Offset and Dephasing Noise

In practical circuits, stray ambient electric fields or intentional gate connections introduce a background charge offset. This transforms the kinetic charging term of the Hamiltonian:

$$\hat{H} = 4 E_c (\hat{n} - n_g)^2 - E_J \cos \hat{\phi}$$

Where $n_g = \frac{C_g V_g}{2e}$ represents the continuous external dimensionless offset charge.

[Image plotting energy levels of a charge qubit versus offset charge ng showing high charge dispersion curves]

### The Charge Noise Problem

When the charging energy is comparable to or larger than the Josephson energy ($E_J \lesssim E_c$), the system operates in the **Cooper-Pair Box** regime. In this regime, the system's energy levels depend heavily on the value of $n_g$.

Small background fluctuations in $n_g$ caused by moving trap charges in the substrate shift the qubit's transition frequency over time. This variation causes the quantum phase to drift, leading to rapid **charge-noise dephasing** and short coherence times ($T_2$).

---

## 10. The Transmon Regime

The **Transmon Qubit** resolves this charge-noise sensitivity by shunting the Josephson junction with a large parallel capacitor. This significantly increases the total capacitance $C$, lowering the characteristic charging energy $E_c$.

```
                  COOPER-PAIR BOX                          TRANSMON REGIME
          +-------------------------------+       +-------------------------------+
          |   • EJ / EC ~ 1               |       |   • EJ / EC >> 1  (~50)       |
          |   • High Anharmonicity        | ----> |   • Lower Anharmonicity       |
          |   | High Charge Dispersion    |       |   • Flat Energy Bands         |
          |   • Short Coherence Time      |       |   • Long Coherence Time       |
          +-------------------------------+       +-------------------------------+

```

The transmon is defined by engineering this ratio to be large:

$$\frac{E_J}{E_c} \gg 1 \quad (\text{Typical Target: } \frac{E_J}{E_c} \approx 50)$$

### Suppression of Charge Dispersion

As the ratio $\frac{E_J}{E_c}$ increases, the charge dispersion (the dependence of the energy levels on $n_g$) decreases exponentially:

$$\epsilon_m \propto \exp\left(-\sqrt{8 E_J / E_c}\right)$$

This flattens the energy bands relative to $n_g$. Because the energy bands are flat, fluctuations in background charge no longer shift the qubit's transition frequency, protecting the system from charge-noise dephasing.

### Taylor Series Expansion of the Potential

Because $E_J \gg E_c$, the phase variable $\phi$ is tightly confined near zero. This allows us to approximate the cosine potential using a Taylor series expansion:

$$\cos \hat{\phi} \approx 1 - \frac{\hat{\phi}^2}{2} + \frac{\hat{\phi}^4}{24} - \dots$$

Substituting this expansion back into the Hamiltonian:

$$\hat{H} \approx 4 E_c (\hat{n} - n_g)^2 + \frac{1}{2} E_J \hat{\phi}^2 - \frac{1}{24} E_J \hat{\phi}^4$$

* **Harmonic Base ($\frac{1}{2} E_J \hat{\phi}^2$):** Combined with the kinetic charging term, this forms a baseline harmonic oscillator profile with a plasma frequency $\omega_p \approx \sqrt{8 E_c E_J}$.
* **Anharmonic Perturbation ($-\frac{1}{24} E_J \hat{\phi}^4$):** This higher-order term introduces a weak negative nonlinearity, pulling down the higher energy levels.

### Dressed Multi-Level Energy Distribution

Transforming this expanded relation into standard creation and annihilation operators yields a weakly anharmonic multi-level spectrum:

$$E_n \approx \hbar \omega_p \left(n + \frac{1}{2}\right) - \frac{E_c}{2} \left(n^2 + n\right)$$

The transition energies between consecutive levels are no longer equal:

* $E_{10} = E_1 - E_0 = \hbar \omega_p - E_c$
* $E_{21} = E_2 - E_1 = \hbar \omega_p - 2E_c$

The difference between these transition energies is the **Anharmonicity ($\alpha$)**:

$$\alpha = E_{21} - E_{10} = -E_c$$

For a typical transmon, $E_c \approx 300\ \text{MHz}$. This provides enough anharmonicity to cleanly address the $|0\rangle \to |1\rangle$ transition using short microwave pulses ($\sim 10 - 20\ \text{ns}$) without driving the system into higher-order states.

---

## 11. Architectural Families of Superconducting Qubits

Superconducting qubits can be engineered into different families by varying the balance between charging energy, Josephson energy, and inductive configurations.

[Image comparing schematic circuit diagrams for a Charge, Transmon, Flux, and Fluxonium qubit]

### 1. The Transmon

* **Ratio:** $E_J / E_c \gg 1$
* **Design:** Uses a large shunted capacitance to minimize charge noise at the cost of working with weak anharmonicity.

### 2. The Flux Qubit

* **Ratio:** $E_J / E_c \sim 10$
* **Design:** Uses a superconducting loop interrupted by multiple junctions. The system is biased with an external magnetic flux ($\Phi_{\text{ext}} = \frac{\Phi_0}{2}$), storing information in the direction of persistent circulating supercurrents (clockwise vs. counter-clockwise). This provides high anharmonicity but increases sensitivity to magnetic flux noise.

### 3. The Fluxonium

* **Ratio:** Includes a large shunting array of Josephson junctions that act as a superinductance.
* **Design:** The large inductance bypasses low-frequency charge noise while maintaining high anharmonicity and a large flux-tuning range, helping protect the qubit's coherence.

---

## 12. Core Mathematical Reference Sheet

$$\begin{array}{ll}
\hline
\textbf{Parameter Metric} & \textbf{Mathematical Formulation} \\ \hline
\text{First Josephson Relation (Current)} & I = I_c \sin \phi \\
\text{Second Josephson Relation (Voltage)} & V = \frac{\Phi_0}{2\pi} \frac{d\phi}{dt} = \frac{\hbar}{2e} \frac{d\phi}{dt} \\
\text{Variable Josephson Inductance} & L_J(\phi) = \frac{\Phi_0}{2\pi I_c \cos \phi} = \frac{L_{J0}}{\cos \phi} \\
\text{Junction Potential Energy Well} & E(\phi) = E_J(1 - \cos \phi) \quad \left(\text{where } E_J = \frac{I_c \Phi_0}{2\pi}\right) \\
\text{Canonical Quantization Operator} & [\hat{\phi}, \hat{n}] = j \\
\text{Total Qubit Circuit Hamiltonian} & \hat{H} = 4E_c(\hat{n} - n_g)^2 - E_J \cos \hat{\phi} \quad \left(\text{where } E_c = \frac{e^2}{2C}\right) \\
\text{Transmon Anharmonicity Index} & \alpha = E_{21} - E_{10} = -E_c \\ \hline
\end{array}$$