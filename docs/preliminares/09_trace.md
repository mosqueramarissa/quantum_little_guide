# 0.9 The trace

La traza de una matriz cuadrada $A$, denotada como $	ext{Tr}(A)$, se define como la suma de los elementos de su diagonal principal. En notación de Dirac con respecto a una base ortonormal $\{\vert{}i\rangle\}$:

$$	ext{Tr}(A) = \sum_i \langle i\vert{}A\vert{}i\rangle$$

Una de las propiedades más importantes de la traza en la mecánica cuántica es su invarianza cíclica. Para matrices $A$, $B$ y $C$ (asumiendo que sus dimensiones permiten la multiplicación):

$$	ext{Tr}(ABC) = 	ext{Tr}(CAB) = 	ext{Tr}(BCA)$$

Esta propiedad es especialmente útil al evaluar productos externos. La traza del producto externo entre un ket y un bra resulta ser igual a su producto interno:

$$	ext{Tr}(\vert{}\psi\rangle\langle\phi\vert{}) = \langle\phi\vert{}\psi\rangle$$

La traza también es invariante bajo cambios de base (transformaciones unitarias), lo que significa que $	ext{Tr}(U A U^\dagger) = 	ext{Tr}(A)$, haciendo de la traza una herramienta independiente del sistema de coordenadas elegido.

---
**Referencias**
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information*. Cambridge University Press. (Capítulo 2: Linear Algebra).
