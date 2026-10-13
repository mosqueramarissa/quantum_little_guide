# Capítulo 4: El Formalismo Estabilizador y la Extracción de Síndromes

Para proteger la información cuántica, no podemos simplemente "medir" los qubits para ver si han sufrido un error, ya que esto provocaría el colapso de su estado de superposición. La solución radica en trasladar el concepto de matrices de comprobación de paridad clásicas al ámbito cuántico mediante mediciones colectivas que extraen información sobre los errores sin revelar la información contenida en los qubits lógicos. Este marco matemático es el formalismo estabilizador.

## 4.1 El Grupo de Pauli sobre $n$ Qubits ($P_n$)

El formalismo estabilizador se fundamenta en los operadores de Pauli. Para un solo qubit, el grupo de Pauli $P_1$ consiste en las matrices $\{I, X, Y, Z\}$ junto con los factores de fase globales $\{\pm 1, \pm i\}$. 

Para un sistema de $n$ qubits, el grupo de Pauli $P_n$ se forma mediante todos los posibles productos tensoriales de $n$ matrices de Pauli, multiplicados por los factores de fase:
$$P_n = \{\pm 1, \pm i\} \times \{I, X, Y, Z\}^{\otimes n}$$

Una propiedad fundamental de dos operadores cualesquiera $A, B \in P_n$ es que, o bien conmutan ($AB = BA$), o bien anticonmutan ($AB = -BA$).

## 4.2 El Grupo Estabilizador ($S$) y el Espacio del Código

Un código estabilizador se define seleccionando un subgrupo abeliano $S$ del grupo de Pauli $P_n$. Para que este subgrupo defina un código cuántico válido, debe cumplir dos condiciones:
1. Todos los elementos en $S$ deben conmutar entre sí (es un subgrupo abeliano).
2. El operador $-I$ no debe pertenecer a $S$ (para garantizar que exista un subespacio no trivial).

El **espacio del código** $V_S$ se define como el subespacio vectorial simultáneo de eigenvectores con eigenvalor $+1$ para todos los operadores en $S$. Es decir, un estado $\vert{}\psi\rangle$ es un estado lógico válido si y solo si:
$$M \vert{}\psi\rangle = \vert{}\psi\rangle \quad \forall M \in S$$

Si queremos codificar $k$ qubits lógicos en $n$ qubits físicos, el subgrupo estabilizador $S$ debe ser generado por $n - k$ generadores independientes $M_1, M_2, \dots, M_{n-k}$. Estos generadores actúan como el análogo cuántico de las filas en una matriz de comprobación de paridad $H$.

## 4.3 Errores y Extracción de Síndromes

Supongamos que un estado válido $\vert{}\psi\rangle$ sufre un error representado por un operador de Pauli $E$. El estado corrompido es $E\vert{}\psi\rangle$. 

Para detectar este error, medimos los observables correspondientes a los generadores del estabilizador $M_i$. Dado que $M_i$ y $E$ pertenecen a $P_n$, o bien conmutan o anticonmutan.
*   **Si $M_i$ y $E$ conmutan ($M_i E = E M_i$):**
    $$M_i (E\vert{}\psi\rangle) = E (M_i\vert{}\psi\rangle) = E (\vert{}\psi\rangle) = +1 (E\vert{}\psi\rangle)$$
    La medición de $M_i$ arroja un resultado determinista de $+1$.
*   **Si $M_i$ y $E$ anticonmutan ($M_i E = -E M_i$):**
    $$M_i (E\vert{}\psi\rangle) = -E (M_i\vert{}\psi\rangle) = -E (\vert{}\psi\rangle) = -1 (E\vert{}\psi\rangle)$$
    La medición de $M_i$ arroja un resultado determinista de $-1$.

La secuencia de resultados de medición de los $n-k$ generadores (transformando los resultados $\pm 1$ en bits clásicos $0, 1$) constituye el **síndrome** del error[cite: 1, 2]. Al medir $M_i$, proyectamos el sistema en un subespacio de eigenvalores sin perturbar las superposiciones que representan la información lógica.

---

## Práctica Interactiva: Simulador de Clifford para Extracción de Síndromes

Para implementar módulos de simulación de circuitos de extracción de síndromes[cite: 2], es imperativo modelar computacionalmente la medición de estabilizadores. Este código implementa un circuito básico que extrae el síndrome de paridad $Z \otimes Z$ para dos qubits utilizando un qubit ancilla, demostrando cómo un error $X$ genera un síndrome negativo (o bit $1$).

```python
import numpy as np
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit.quantum_info import Statevector

# Definimos los registros: 2 qubits de datos y 1 qubit ancilla para medir el síndrome ZZ
q_data = QuantumRegister(2, 'data')
q_ancilla = QuantumRegister(1, 'ancilla')
c_syndrome = ClassicalRegister(1, 'syndrome')
qc = QuantumCircuit(q_data, q_ancilla, c_syndrome)

# 1. Preparación de un estado válido (Eigenestado +1 de ZZ)
# Por ejemplo, el estado |00>. (ZZ|00> = |00>)
qc.id(q_data) # El estado inicial por defecto es |000>

# 2. Inyección de un error de Pauli en el qubit 0 (anticonmuta con ZZ)
print("Inyectando error X en el qubit de datos 0...")
qc.x(q_data[0]) 

qc.barrier()

# 3. Circuito de extracción de síndrome para el estabilizador Z_0 Z_1
# El ancilla se inicializa en |0>. Usamos compuertas CNOT donde el ancilla es el objetivo.
qc.cx(q_data[0], q_ancilla[0])
qc.cx(q_data[1], q_ancilla[0])

# 4. Medición del ancilla
qc.measure(q_ancilla, c_syndrome)

# 5. Simulación
from qiskit_aer import AerSimulator
simulator = AerSimulator()
result = simulator.run(qc, shots=1).result()
counts = result.get_counts(qc)

print("Circuito de extracción de síndrome:")
print(qc.draw())
print(f"Resultado de la medición del síndrome (0 indica eigenvalor +1, 1 indica eigenvalor -1): {counts}")
# El resultado será '1', detectando el error X porque X y Z anticonmutan.
```

### Referencias bibliográficas
Gottesman, D. (1997). Stabilizer Codes and Quantum Error Correction, Tesis doctoral, California Institute of Technology.
Gottesman, D. (2010). "An introduction to quantum error correction and fault-tolerant quantum computation", en Quantum Information Science and Its Contributions to Mathematics[cite: 1].
Preskill, J. Quantum Computation, California Institute of Technology[cite: 1].
Nielsen, M. A., & Chuang, I. L. (2010). Quantum Computation and Quantum Information, Cambridge University Press[cite: 1].