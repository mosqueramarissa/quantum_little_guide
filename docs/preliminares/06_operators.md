# 0.6 Operators

En mecánica cuántica, los cambios y transformaciones sobre los estados de un sistema se describen mediante operadores lineales. Un operador lineal $A$ actúa sobre el espacio de Hilbert $\mathcal{H}$, tomando un estado ket y mapeándolo a otro:

$$A|\psi\rangle = |\phi\rangle$$

Matricialmente, si el espacio de estados tiene dimensión $n$, el operador se representa como una matriz compleja de $n \times n$. Existen dos clases de operadores que son de vital importancia en la información cuántica:

**Operadores Unitarios ($U$)**
Garantizan que la evolución de un sistema cuántico cerrado conserve la normalización de las probabilidades. Se definen por la propiedad de que su adjunto es igual a su inversa:

$$U^\dagger U = U U^\dagger = I$$

Donde $I$ es la matriz identidad. Las compuertas cuánticas (como la compuerta Hadamard o Pauli-X) son, por definición, operadores unitarios.

**Operadores Hermitianos (o Auto-adjuntos)**
Representan variables observables físicamente mensurables (como la energía, la posición, o el espín). Un operador $H$ es Hermitiano si es igual a su propio adjunto:

$$H = H^\dagger$$

Esta propiedad asegura que los resultados de cualquier medición cuántica sean siempre valores reales.

---
**Referencias**
* Von Neumann, J. (1932). *Mathematical Foundations of Quantum Mechanics*. Princeton University Press. (Capítulo 2: Abstract Hilbert Space).
