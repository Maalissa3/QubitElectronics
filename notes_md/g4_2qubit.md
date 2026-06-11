This guide offers an in-depth, structured explanation of the lecture notes from Professor Mariagrazia Graziano (Politecnico di Torino). It details the physics, architecture, and electronic control systems required to build and operate semiconductor-based quantum computers.

---

## 1. Qubit Encoding Physics: Spin vs. Charge

Semiconductor qubits are typically realized using **Quantum Dots (QDs)**—nano-scale semiconductor structures (often in Silicon or Gallium Arsenide) that trap individual electrons using electrostatic gates.

### Spin Qubits

* **Encoding:** The logical states $|0\rangle$ and $|1\rangle$ map directly to the intrinsic magnetic dipole moment of a single electron trapped in a quantum dot: spin-up ($|\uparrow\rangle$) and spin-down ($|\downarrow\rangle$).
* **Coherence Time ($T_2$):** Relatively long ($\sim \text{tens of }\mu\text{s}$ in GaAs, extending to milliseconds or seconds in isotopically purified $^{28}\text{Si}$). Spin states are well-isolated from environmental electric noise.
* **Manipulation Time:** Relatively slow ($\sim \text{tens of ns}$), limited by the strength of oscillating magnetic fields or spin-orbit coupling available to drive transitions.

### Charge Qubits

* **Encoding:** The qubit is encoded across a **Double Quantum Dot (DQD)** system. The state depends on the spatial position of a single electron:
* $|L\rangle$: Electron resides in the Left dot.
* $|R\rangle$: Electron resides in the Right dot.


* **Coherence Time ($T_2$):** Short ($\sim \text{tens of ns}$). Because the qubit is based on charge position, it is highly susceptible to charge noise and voltage fluctuations in the surrounding substrate.
* **Manipulation Time:** Extremely fast ($\sim \text{tens of ps}$). Transitions are driven using fast electric voltage pulses on electrostatic gates, which are much easier to generate dynamically than high-frequency magnetic fields.

### Hybrid (Singlet-Triplet) Qubits

To balance these trade-offs, **hybrid qubits** use both position and spin states. For example, a **Singlet-Triplet ($S\text{-}T_0$) qubit** uses two electrons shared between two dots. The logical states are formed by the anti-symmetric Singlet state $|S\rangle$ and the symmetric, unpolarized Triplet state $|T_0\rangle$:

$$|S\rangle = \frac{1}{\sqrt{2}}(|\uparrow\downarrow\rangle - |\downarrow\uparrow\rangle)$$

$$|T_0\rangle = \frac{1}{\sqrt{2}}(|\uparrow\downarrow\rangle + |\downarrow\uparrow\rangle)$$

This configuration provides protection against global magnetic field fluctuations while allowing rapid, electrically controlled manipulation.

---

## 2. Mathematical Framework of the Charge Qubit

The behavior of an electron in a Double Quantum Dot (DQD) is governed by two main physical parameters:

1. **Detuning ($\varepsilon$):** The electrostatic potential energy difference between the left and right quantum dots ($\varepsilon = E_L - E_R$). This is directly modulated by applying voltages to the surface gates.
2. **Tunnel Coupling ($t_0$):** The quantum mechanical probability amplitude for the electron to tunnel through the potential barrier separating the two dots.

The Hamiltonian system in the localized position basis $\{|L\rangle, |R\rangle\}$ is written as:

$$H = \begin{pmatrix} \frac{\varepsilon}{2} & t_0 \\ t_0 & -\frac{\varepsilon}{2} \end{pmatrix}$$

### Case 1: Zero Tunnel Coupling ($t_0 = 0$)

When the tunnel barrier is infinitely high, the states $|L\rangle$ and $|R\rangle$ are perfectly isolated eigenstates.

* The system energy eigenvalues plot as straight lines when mapped against detuning: $E = \pm\frac{\varepsilon}{2}$.
* At the degeneracy point ($\varepsilon = 0$), the energy levels cross. No quantum superposition can occur because the electron cannot cross the barrier.

### Case 2: Finite Tunnel Coupling ($t_0 > 0$)

When the barrier is lowered, quantum tunneling mixes the localized states. Diagonalizing the Hamiltonian yields the hybridized energy eigenvalues:

$$E = \pm \sqrt{\left(\frac{\varepsilon}{2}\right)^2 + t_0^2}$$

* **Avoided Crossing:** At the zero-detuning point ($\varepsilon = 0$), the energy levels no longer cross. Instead, they split by a minimum energy gap equal to $2t_0$.
* **Eigenstates at $\varepsilon = 0$:** The new ground and excited states form symmetric and anti-symmetric molecular-like superpositions:
* Ground state: $|+\rangle = \frac{1}{\sqrt{2}}(|L\rangle + |R\rangle)$
* Excited state: $|-\rangle = \frac{1}{\sqrt{2}}(|L\rangle - |R\rangle)$



---

## 3. Two-Qubit Coupling Mechanism & Exchange Interaction ($J$)

When two spin qubits (Q0 and Q1) are placed in adjacent quantum dots, their physical proximity causes their wavefunctions to overlap, giving rise to the **Exchange Interaction ($J$)**.

### Physical Principle

According to the Pauli Exclusion Principle, two electrons cannot occupy the exact same quantum state. If two adjacent electrons have **parallel spins** ($|\uparrow\uparrow\rangle$ or $|\downarrow\downarrow\rangle$), Coulomb repulsion forces them apart because spatial overlap is restricted. If they have **anti-parallel spins** ($|\uparrow\downarrow\rangle$ or $|\downarrow\uparrow\rangle$), they can tunnel into a shared orbital state (virtual tunneling).

This energy shift means the energy required to flip the spin of Q0 depends entirely on whether the spin of Q1 is pointing up or down.

### Frequency Shifts

This state-dependent energy shift modifies the Electron Spin Resonance (ESR) frequencies. If you monitor the resonance frequency of Q0, you observe two distinct lines:

* $f_1$: The resonance frequency of $Q_0$ when $Q_1$ is in state $|\downarrow\rangle$.
* $f_2$: The resonance frequency of $Q_0$ when $Q_1$ is in state $|\uparrow\rangle$.

The Exchange Interaction energy parameter $J$ quantified in terms of frequency is:

$$J = f_2 - f_1$$

By tuning the electrostatic gates (modifying detuning $\varepsilon$ or lowering the inter-dot tunnel barrier), you turn $J$ on and off, enabling controlled two-qubit logic operations.

---

## 4. Hardware Implementation: The Veldhorst Architecture

The slides reference a foundational 2015 experiment by Menno Veldhorst et al. (Nature), which demonstrated a two-qubit logic device in Silicon.

### Device Control Topology

The device controls a two-dimensional electron gas using surface metallic gates ($G_1, G_2, G_3$) and a central coupling gate ($G_c$):

* **Plunger Gates ($G_1, G_2$):** Control the local chemical potential (detuning $\varepsilon$) of Qubit 1 and Qubit 2 respectively.
* **Coupling Gate ($G_c$):** Controls the electrostatic barrier between the dots, dynamically turning the exchange interaction $J$ on or off.

### Charge Stability & The "Diamond Diagram"

To initialize the system, operators use a **Charge Stability Diagram** (often called a honeycomb or diamond diagram). This plot maps out the stable electron charge configurations $(N_1, N_2)$—where $N_1$ and $N_2$ represent the exact number of electrons in Dot 1 and Dot 2—as a function of the gate voltages.

By precisely biasing the gates, the system is calibrated to the $(1,1)$ regime, ensuring exactly one active electron occupies each dot.

### Readout via Single-Electron Transistor (SET)

Readout uses a highly sensitive nearby **Single-Electron Transistor (SET)** coupled capacitively to the dots.

* **Spin-to-Charge Conversion:** Because an SET can only detect changes in local electrical charge (not magnetic spin), spin must be converted to charge position first.
* **Sequential Readout:**
* **Q2 Readout:** Gate voltages are pulsed toward the $(1,1) \rightarrow (0,2)$ charge transition boundary. If the spins form a singlet state, tunneling is allowed; if they form a triplet state, Pauli block prevents tunneling. The SET senses whether an electron moved.
* **Q1 Readout:** The system is shifted toward the $(0,1) \rightarrow (0,0)$ boundary, reading out the final electron state.



---

## 5. Timeline of a CNOT Gate Operation

The Controlled-NOT (CNOT) gate is the fundamental building block for universal quantum computation. The experimental process maps to the following timeline:

```
[ Phase 1: Prep ] ---> [ Phase 2: Init Rotation ] ---> [ Phase 3: Entangling ] ---> [ Phase 4: Conditional ] ---> [ Phase 5: Target Finalize ]
  Align Q0 to            Apply π/2 MW Pulse             Shift Detuning to          Q0 Rotates at distinct       Apply 2nd π/2 MW Pulse
  Reservoir (Elzerman)   to Target Qubit (Q0)           Induce Hybridization       Rabi Rates based on Q1       to complete CNOT

```

### Phase 1: Initialization & Spin Alignment

* The target qubit ($Q_0$, Blue) is initialized into a known pure spin state using the **Elzerman Method**.
* The quantum dot energy levels are aligned relative to the Fermi energy ($E_F$) of a nearby electron reservoir. A spin-down electron is energetically favored to stay in the dot, whereas a spin-up electron tunnels out into the reservoir and is instantly replaced by a spin-down electron. This initializes the system to $|\downarrow\rangle$.

### Phase 2: Initial Target Rotation

* At this stage, the two qubits are kept decoupled ($J \approx 0$).
* An oscillating microwave (MW) pulse matching the native resonance frequency of $Q_0$ is applied. This drives a $\pi/2$ rotation (a square-root of NOT operation), placing the blue target qubit into a coherent superposition state:

$$\frac{1}{\sqrt{2}}(|\downarrow\rangle + |\uparrow\rangle)$$



### Phase 3: Controlled Hybridization (The Entangling Step)

* The control electronics apply a rapid voltage pulse to alter the detuning $\varepsilon$, shifting the system's energy levels.
* This brings the state of the blue qubit ($Q_0$) into close resonance with a higher orbital level of the red control qubit ($Q_1$). The wavefunctions mix, turning on the exchange interaction ($J > 0$). The two qubits are now highly entangled.

### Phase 4: Conditional Rabi Oscillations

* Because $J$ is active, the resonance frequency of $Q_0$ splits into two paths depending on $Q_1$'s state:
* If $Q_1 = |\downarrow\rangle$, $Q_0$ precesses at frequency $f_1$.
* If $Q_1 = |\uparrow\rangle$, $Q_0$ precesses at frequency $f_2$.


* As time progresses, the probability wave of $Q_0$ oscillates (Rabi oscillations). The phase accumulation rate is noticeably different for both conditions.
* **The Critical Intercept ($\sim 0.5\,\mu\text{s}$):** The system dwells in this interaction state until exactly $\Delta t = \frac{1}{2J}$ has elapsed. At this precise timestamp, the phase difference between the two configurations reaches exactly $\pi$ radians ($180^\circ$).

### Phase 5: Target Finalization

* The detuning pulse is turned off, dropping $J$ back to zero and stopping the conditional evolution.
* A final $\pi/2$ microwave pulse is applied to $Q_0$.
* If $Q_1$ was $|\downarrow\rangle$, the total phase accumulated causes the final pulse to return $Q_0$ safely to its original state.
* If $Q_1$ was $|\uparrow\rangle$, the extra $\pi$ phase shift causes the final pulse to flip $Q_0$ to the opposite spin state.



This sequence completes the logical target flip conditioned entirely on the control state, completing the **CNOT** gate operation.