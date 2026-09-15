# 0.7 Eigenvalues and eigenvectors

El análisis espectral de los operadores es el puente entre las matemáticas de los espacios vectoriales y los resultados de las mediciones cuánticas. 

Para un operador lineal $A$, un vector no nulo $|v\rangle$ es un **eigenvector** (o vector propio) si, al aplicarle el operador, el vector resultante es solo un múltiplo escalar del original:

$$A|v\rangle = \lambda|v\rangle$$

El escalar $\lambda \in \mathbb{C}$ es el **eigenvalue** (o valor propio) asociado a ese eigenvector.

En el contexto físico de una medición cuántica descrita por un observable Hermitiano, los eigenvalores representan todos los posibles resultados deterministas numéricos de la medición, y siempre son números reales. Los eigenvectores representan los estados en los cuales colapsa el sistema inmediatamente después de ser medido.

Por el teorema espectral, todo operador Hermitiano (así como todo operador unitario) es diagonalizable. Cuando una matriz es diagonalizable, existe una base de eigenvectores en la que dicha matriz toma una forma estrictamente diagonal. En esta representación específica, la diagonal de una matriz son sus eigenvalores, facilitando enormemente el cálculo de la evolución temporal y las probabilidades del sistema.

---
**Referencias**
* Halmos, P. R. (1958). *Finite-Dimensional Vector Spaces*. Springer. (Capítulo 2: Transformations).
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 2: Linear Algebra).
