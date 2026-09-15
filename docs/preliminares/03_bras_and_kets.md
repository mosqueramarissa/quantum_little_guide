# 0.3 Bras and kets

En computación e información cuántica, el estándar para representar estados es la notación de Dirac (o notación bra-ket). Esta notación simplifica el manejo del álgebra lineal en espacios de Hilbert.

Un **ket**, denotado como $|\psi\rangle$, representa un vector columna que describe el estado de un sistema cuántico en el espacio vectorial complejo $\mathcal{H}$. Para un qubit, se expresa matricialmente como:

$$|\psi\rangle = \begin{pmatrix} \alpha \\ \beta \end{pmatrix}$$

Todo espacio vectorial $\mathcal{H}$ tiene asociado un espacio dual $\mathcal{H}^*$. Un **bra**, denotado como $\langle\psi|$, pertenece a este espacio dual y representa un vector fila. Se obtiene tomando la transpuesta conjugada compleja del ket correspondiente:

$$\langle\psi| = \begin{pmatrix} \alpha^* & \beta^* \end{pmatrix}$$

La yuxtaposición de un bra y un ket forma un "bra-ket" $\langle\phi|\psi\rangle$, el cual denota el producto interno (escalar) entre los estados $|\phi\rangle$ y $|\psi\rangle$. El resultado de esta operación es un número complejo que representa la amplitud de probabilidad de que el estado $|\psi\rangle$ colapse al ser medido en el estado $|\phi\rangle$.

---
**Referencias**
* Dirac, P. A. M. (1939). *A New Notation for Quantum Mechanics*. Mathematical Proceedings of the Cambridge Philosophical Society.
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 2: Linear Algebra).
