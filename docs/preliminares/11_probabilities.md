# 0.11 Probabilities

La conexión entre el formalismo matemático de la mecánica cuántica y el mundo físico observable se rige por la Regla de Born. 

Si medimos un estado cuántico $\vert{}\psi\rangle$ utilizando un conjunto de operadores de medición $\{M_m\}$, donde $m$ denota los posibles resultados, la probabilidad de obtener el resultado $m$ está dada por:

$$p(m) = \langle\psi\vert{} M_m^\dagger M_m \vert{}\psi\rangle$$

Para que estas probabilidades sean físicamente válidas, deben sumar a 1, lo que impone la condición de completitud sobre los operadores de medición: $\sum_m M_m^\dagger M_m = I$.

Si la medición se realiza sobre una base ortonormal específica, digamos $\{\vert{}m\rangle\}$, los operadores son proyectores de la forma $M_m = \vert{}m\rangle\langle m\vert{}$. La Regla de Born se reduce entonces a la amplitud de probabilidad al cuadrado:

$$p(m) = \vert{}\langle m\vert{}\psi\rangle\vert{}^2$$

Finalmente, el valor esperado (o promedio estadístico) de un observable descrito por un operador Hermitiano $A$ sobre un estado $\vert{}\psi\rangle$ se calcula de forma compacta como:

$$\langle A \rangle = \langle\psi\vert{}A\vert{}\psi\rangle$$

---
**Referencias**
* Born, M. (1926). *Zur Quantenmechanik der Stoßvorgänge*. Zeitschrift für Physik.
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information*. Cambridge University Press. (Capítulo 2: Postulates of Quantum Mechanics).
