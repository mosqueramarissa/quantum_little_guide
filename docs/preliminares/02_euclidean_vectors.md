# 0.2 Euclidean vectors and vector spaces

Un espacio vectorial sobre los números complejos consiste en un conjunto de vectores donde están definidas las operaciones de suma y multiplicación por un escalar complejo. En información cuántica, trabajamos específicamente con espacios de Hilbert de dimensión finita, que son espacios vectoriales complejos dotados de un producto interno.

Para definir un sistema, requerimos establecer una base. Los estados base son vectores linealmente independientes que componen el espacio vectorial de tal manera que cualquier vector del sistema puede ser expresado como una combinación lineal de ellos. Si un espacio tiene dimensión $n$, una base estará conformada exactamente por $n$ vectores $\{v_1, v_2, \dots, v_n\}$.

Un vector genérico en el espacio puede escribirse como una matriz columna (en un espacio de dimensión 2, correspondiente a un qubit):

$$
v = \begin{pmatrix} v_1 \\ v_2 \end{pmatrix}
$$

**Producto Interno y Ortogonalidad**
El producto interno (o escalar) toma dos vectores y produce un número complejo. Este define la geometría del espacio. Un concepto crucial geométrico y físico es la ortogonalidad: se dice que el producto interno de dos vectores es cero cuando estos son mutuamente excluyentes, es decir, son ortogonales. Si además tienen una magnitud (norma) igual a 1, forman un conjunto ortonormal.

---
**Referencias**
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 2: Linear Algebra).
