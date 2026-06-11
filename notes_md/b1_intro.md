# Qubit Introduction
## What is a qubit?
A qubit, or quantum bit, is the fundamental unit of quantum information. It is a two-level quantum system that can exist in a superposition of states, unlike a classical bit which can only be in one of two states (0 or 1). A qubit can be represented as:
$$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle
$$
where $\alpha$ and $\beta$ are complex numbers that satisfy the normalization condition $|\alpha|^2 + |\beta|^2 = 1$.

# Computers and Computation

## Models of classical computation
Classical computation can be realized with different physical technologies:
- Mechanical
- Electrical
- Optical
- Biological

### Examples
- Curta calculator
- Digi-Comp I
- Babbage’s difference engine

## Models of computation
Computing can be described using:
- **Physical models**: mechanical, optical, electrical, biological
- **Conceptual models**:
  - Turing machine
  - Cellular automata
  - Von Neumann architecture
  - Logic-in-memory

### Von Neumann architecture
Main components:
- CPU
- Memory
- Bus
- I/O

## Moore’s Law
Historically, transistor density and performance improved rapidly over time, driving classical hardware scaling.

## Quantum history
- In the early 1980s, Richard Feynman suggested that quantum systems should be simulated with quantum systems.
- This motivated the idea of a quantum computer.
- Important milestones in the 1990s:
  - Shor’s algorithm
  - First quantum error-correcting code

### Current status
- Quantum processors with more than 100 qubits already exist.
- IBM Condor is reported to have more than 1000 qubits.

## Main quantum computer players
Quantum computing is being developed by several major industrial and academic groups.

---

# Data Representation

## Classical computers
- Classical computers process information using **transistors**.
- Information is encoded in bits:
  - `0`
  - `1`

## Quantum computers
- Quantum computers use **qubits**.
- A qubit can be in a superposition:
  
  `|ψ⟩ = α|0⟩ + β|1⟩`

- If `|α|² = |β|² = 1/2`, then the qubit is measured as:
  - 50% `|0⟩`
  - 50% `|1⟩`

## Classical parallelism
- `N` classical bits represent one `N`-bit state.
- Example for `N = 3`:
  - `000`, `001`, ..., `111`
- A classical function must be evaluated on each input separately.

## Quantum parallelism
- `N` qubits describe a state with `2^N` components.
- Example for `N = 3`:

```text
|ψ⟩ = c1|000⟩ + c2|001⟩ + c3|010⟩ + c4|011⟩ + c5|100⟩ + c6|101⟩ + c7|110⟩ + c8|111⟩
```
## DiVincenzo Criteria

### Requirements of a classical logic element
A classical logic element should:
- Scale well for complex systems
- Be characterizable and manufacturable on a large scale
- Represent classical information reliably
- Allow state initialization and measurement
- Provide gain and noise margins
- Be robust against noise and failure

**Transistors** satisfy these requirements.

### What makes a good qubit?
A good qubit should:
- Be a scalable physical system
- Allow initialization of the input state
- Have long coherence times
- Support a universal set of gates
- Represent quantum information with high fidelity
- Allow conversion between stationary and flying qubits
- Allow faithful transmission of flying qubits between locations

---

## Qubit Figures of Merit

### Coherence time
Coherence time is the time before quantum information is lost due to interaction with the environment.

Main mechanisms:
- Energy relaxation: `T1`
- Dephasing: `Tφ`

The relation is:

`1/T2 = 1/Tφ + 1/(2T1)`

### Gate speed
- **Gate time**: time required to perform a quantum operation
- A useful figure of merit is the number of gates that can be performed within the qubit lifetime:

`N_gates ~ T2 / t_gate`

### Gate fidelity
Gate fidelity measures how accurately a gate is performed.
- High fidelity is essential for reliable quantum computation
- It can be measured by:
  - Process tomography
  - Randomized benchmarking

---

## Bits and Qubits

### Classical computers
- Classical computers use Boolean logic
- Universal classical computation can be built from single- and two-bit gates such as NOT and AND

### Quantum computers
- Quantum computers use quantum logic
- A qubit can be in superposition:

`|ψ⟩ = α|0⟩ + β|1⟩`

### X gate
The quantum NOT gate is the **X gate**:
- `|0⟩ → |1⟩`
- `|1⟩ → |0⟩`

For a general state:

`α|0⟩ + β|1⟩ → β|0⟩ + α|1⟩`

### Circuits in space vs circuits in time
#### Classical logic
- Gates act on wires in space
- Input and output are at different physical locations

#### Quantum logic
- Gates act on qubits in time
- The same qubit evolves during the operation

---

## Two-Qubit Gates

### Classical XOR
For two bits `A` and `B`:

| A | B | A ⊕ B |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

### Quantum CNOT
The quantum analogue is the **CNOT gate**.

If the control qubit is in superposition, CNOT can create entanglement.

For example, if the control qubit is in a superposition and the target is `|0⟩`:

`|ψ_in⟩ = (|0⟩ + |1⟩)_A |0⟩_B`

then:

`|ψ_out⟩ = |0⟩_A|0⟩_B + |1⟩_A|1⟩_B`

---

## Qubit States

A qubit is described by:

`|ψ⟩ = α|0⟩ + β|1⟩`

where:
- `α` and `β` are complex numbers
- normalization requires:

`|α|² + |β|² = 1`

### Bra-ket notation
- `|ψ⟩` is a ket
- `⟨ψ|` is its Hermitian conjugate, or bra
- The inner product is written as `⟨φ|ψ⟩`

---

## Measurement of Qubits

### Measurement process
- Measurement is an active process
- The measurement apparatus interacts with the qubit
- Only partial information is obtained
- The full quantum state is not directly accessible

### Measurement depends on a basis
Most measurements are performed in the computational basis:

`{|0⟩, |1⟩}`

### State collapse
After measurement, the qubit collapses to one basis state:
- `|0⟩`
- `|1⟩`

This is called **Z-measurement**.

### Born rule
The probability of an outcome is the squared modulus of its amplitude.

For `|ψ⟩ = α|0⟩ + β|1⟩`:
- `P(0) = |α|²`
- `P(1) = |β|²`

### Other common measurement bases
#### X basis
- `|+⟩ = (|0⟩ + |1⟩)/√2`
- `|−⟩ = (|0⟩ - |1⟩)/√2`

#### Y basis
- `|+i⟩ = (|0⟩ + i|1⟩)/√2`
- `|−i⟩ = (|0⟩ - i|1⟩)/√2`

---

## Bloch Sphere

Any single-qubit pure state can be written as:

`|ψ⟩ = cos(θ/2)|0⟩ + e^{iφ} sin(θ/2)|1⟩`

where:
- `θ ∈ [0, π]`
- `φ ∈ [0, 2π]`

The Bloch vector is:

`r = (sinθ cosφ, sinθ sinφ, cosθ)`

### Examples
- `|0⟩` → `r = (0, 0, 1)`
- `|1⟩` → `r = (0, 0, -1)`
- `|+⟩` → `r = (1, 0, 0)`
- `|−⟩` → `r = (-1, 0, 0)`

---

## Unitary Operations and Single-Qubit Gates

### Single-qubit gates
A single-qubit gate transforms one quantum state into another:

`|ψ'⟩ = U|ψ⟩`

To preserve normalization, `U` must be **unitary**.

### Unitary matrices
A matrix `U` is unitary if:

`U†U = UU† = I`

Properties:
- Preserves total probability
- Preserves inner products
- Is reversible
- Does not destroy information

---

## Common Single-Qubit Gates

### Pauli gates
#### Pauli-X
`X = [[0, 1], [1, 0]]`

- `X|0⟩ = |1⟩`
- `X|1⟩ = |0⟩`

Interpretation:
- Rotation around the x-axis by `π`

#### Pauli-Y
`Y = [[0, -i], [i, 0]]`

Interpretation:
- Rotation around the y-axis by `π`

#### Pauli-Z
`Z = [[1, 0], [0, -1]]`

- `Z|+⟩ = |−⟩`

Interpretation:
- Rotation around the z-axis by `π`

### Pauli matrix properties
- `X² = Y² = Z² = I`
- All Pauli matrices are Hermitian
- Commutation relations:
  - `[X, Y] = 2iZ`
  - `[Y, Z] = 2iX`
  - `[Z, X] = 2iY`
- Anticommutation relations:
  - `{X, Y} = 0`
  - `{Y, Z} = 0`
  - `{Z, X} = 0`

---

## Rotation Gates

Rotation around axis `n`:

`R_n(θ) = e^{-iθσ_n/2}`

In particular:
- `R_x(θ) = cos(θ/2)I - i sin(θ/2)X`
- `R_y(θ) = cos(θ/2)I - i sin(θ/2)Y`
- `R_z(θ) = cos(θ/2)I - i sin(θ/2)Z`

When `θ = π`, these reduce to the corresponding Pauli operations.

---

## Hadamard Gate

The Hadamard gate is:

`H = (1/√2) [[1, 1], [1, -1]]`

### Action
- `H|0⟩ = |+⟩`
- `H|1⟩ = |−⟩`

### Role
- Creates superposition
- Changes basis between Z and X bases

---

## Phase Gate

The S gate is the phase gate:

`S = [[1, 0], [0, i]]`

### Action
- `S|+⟩ = |+i⟩`
- `S|−⟩ = |−i⟩`

### Role
- Adds a 90° phase shift
- Helps change between Z and Y bases

---

## Applying Multiple Gates
Multiple gates are applied sequentially using matrix multiplication.

Matrix expressions act **right to left**, while circuit diagrams are read **left to right**.

Example:

`X Y H |0⟩`

means:
1. Start in `|0⟩`
2. Apply `H`
3. Apply `Y`
4. Apply `X`

---

## Density Matrix

### Why it is needed
Real systems are not perfectly isolated, so pure states are not always sufficient.  
A more general description is given by the **density matrix** `ρ`.

### Pure state
For a pure state:

`ρ = |ψ⟩⟨ψ|`

### General properties
- `ρ` is a `2 × 2` matrix for a qubit
- `ρ` is Hermitian
- `tr(ρ) = 1`

### Matrix form
For a qubit:

`ρ = [[ρ11, ρ12], [ρ21, ρ22]]`

- Diagonal terms: populations of `|0⟩` and `|1⟩`
- Off-diagonal terms: coherences
- Hermiticity implies:

`ρ12 = ρ21*`

### Mixed state
If the qubit is in a probabilistic mixture:

`ρ = Σ_i p_i |ψ_i⟩⟨ψ_i|`

### Pure vs mixed states
A useful indicator is:

- `tr(ρ²) = 1` for a pure state
- `tr(ρ²) < 1` for a mixed state