# 0.8 Outer products

Mientras que el producto interno (bra-ket, $\langle\phi|\psi\rangle$) resulta en un escalar, el producto externo (ket-bra) resulta en un operador lineal. 

El producto externo entre un ket $|\phi\rangle$ y un bra $\langle\psi|$ se denota como:

$$|\phi\rangle\langle\psi|$$

Para entender su funcionamiento, basta aplicarlo sobre un estado arbitrario $|\chi\rangle$. Por asociatividad del álgebra, el resultado es el vector $|\phi\rangle$ escalado por el número complejo resultante del producto interno $\langle\psi|\chi\rangle$:

$$(|\phi\rangle\langle\psi|)|\chi\rangle = |\phi\rangle(\langle\psi|\chi\rangle) = \langle\psi|\chi\rangle |\phi\rangle$$

**Proyectores y Relación de Completitud**
Un caso fundamental del producto externo es el proyector ortogonal sobre el subespacio definido por un estado normalizado $|\psi\rangle$, denotado como $P = |\psi\rangle\langle\psi|$. 

Si tomamos un conjunto completo de estados base ortonormales $\{|i\rangle\}$, la suma de sus productos externos forma la relación de completitud (o resolución de la identidad):

$$\sum_i |i\rangle\langle i| = I$$

Esta identidad es una herramienta algebraica indispensable que se inserta frecuentemente entre operadores para simplificar expresiones o realizar cambios de base en mecánia cuántica.

---
**Referencias**
* Dirac, P. A. M. (1958). *The Principles of Quantum Mechanics* (4th ed.). Oxford University Press.
