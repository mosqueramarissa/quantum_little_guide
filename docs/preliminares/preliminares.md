# 0.1 Complex numbers

La mecánica cuántica se rige sobre el cuerpo de los números complejos, $\mathbb{C}$. Un número complejo es la combinación de un número real con un imagainario. Mientras un número real es cualquier numéro que puedas pensar, como:

$$ 1, \quad 13.5, \quad -0.768, \quad \frac{3}{2}, \quad \sqrt{2}$$

Los números imaginarios son aquellos que cuándo se elevan al cuadrado dan un resultado *negativo*. La unidad de un número imaginario es $i$

$$i = \sqrt{-1}$$

Cuando elevamos $i$ al cudrado sucecede lo siguiente:

$$i^2 = -1$$

Volviendo a los números complejo, así se ven,

$$ 1 + i, \quad 43 + 2i, \quad 0.95 - 3.2i, \quad -2 + \pi i$$

Entonces, ya tenemos nuestra primera definición:


$$z = a + bi$$

Donde $a, b \in \mathbb{R}$. La letra $z$ es usada a menudo para representar a los números complejos. Nos referirimos a la parte real y a la imaginaria usando **Re** e **Im** de esta manera:
$$\begin{align*}
    Re(z) &= a
    Im(z) &= b
\begin{align*}$$

Una herramienta fundamental en la computación cuántica es el conjugado complejo, denotado como $z^*$, el cual invierte el signo de la parte imaginaria.

$$z^* = x - bi$$


El módulo (también denominada magnitud) al cuadrado de un número complejo nos permite calcular probabilidades en sistemas cuánticos, y se define como el producto del número por su conjugado complejo:

$$|z|^2 = z z^* = x^2 + y^2$$

También es común representar los números complejos en su forma polar, usando la fórmula de Euler ($e^{i\theta} = \cos\theta + i\sin\theta$):

$$z = r e^{i\theta}$$

Donde $r = |z|$ es la magnitud y $\theta$ es la fase. Las fases relativas entre estados cuánticos son responsables de fenómenos como la interferencia, la cual es base para los algoritmos cuánticos acelerados.

---
### Referencias bibliográficas

* Pierce, Rod. (2026). "Complex Numbers". Math Is Fun. Retrieved 22 Sep 2026 from https://www.mathsisfun.com/numbers/complex-numbers.html
* Rieffel, E. G., & Polak, W. H. (2011). *Quantum Computing: A Gentle Introduction*. MIT Press. (Capítulo 2: Quantum Mechanics Basics).

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
### Referencias bibliográficas

* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 2: Linear Algebra).


# 0.3 Bras and kets

En computación e información cuántica, el estándar para representar estados es la notación de Dirac (o notación bra-ket). Esta notación simplifica el manejo del álgebra lineal en espacios de Hilbert.

Un **ket**, denotado como $|\psi\rangle$, representa un vector columna que describe el estado de un sistema cuántico en el espacio vectorial complejo $\mathcal{H}$. Para un qubit, se expresa matricialmente como:

$$|\psi\rangle = \begin{pmatrix} \alpha \\ \beta \end{pmatrix}$$

Todo espacio vectorial $\mathcal{H}$ tiene asociado un espacio dual $\mathcal{H}^*$. Un **bra**, denotado como $\langle\psi|$, pertenece a este espacio dual y representa un vector fila. Se obtiene tomando la transpuesta conjugada compleja del ket correspondiente:

$$\langle\psi| = \begin{pmatrix} \alpha^* & \beta^* \end{pmatrix}$$

La yuxtaposición de un bra y un ket forma un "bra-ket" $\langle\phi|\psi\rangle$, el cual denota el producto interno (escalar) entre los estados $|\phi\rangle$ y $|\psi\rangle$. El resultado de esta operación es un número complejo que representa la amplitud de probabilidad de que el estado $|\psi\rangle$ colapse al ser medido en el estado $|\phi\rangle$.

---
### Referencias bibliográficas

* Dirac, P. A. M. (1939). *A New Notation for Quantum Mechanics*. Mathematical Proceedings of the Cambridge Philosophical Society.
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 2: Linear Algebra).

# 0.4 Daggers

El operador "daga" (dagger), denotado por el símbolo $\dagger$, es fundamental para conectar kets con bras y operadores con sus adjuntos. Matemáticamente, representa la operación de transposición conjugada (o adjunto hermitiano).

Para cualquier matriz $A$, su matriz adjunta $A^\dagger$ se define tomando la matriz transpuesta y aplicando el conjugado complejo a cada una de sus entradas:

$$A^\dagger = (A^T)^*$$

Como se estableció en la notación de Dirac, aplicar la operación daga a un ket produce el bra correspondiente, y viceversa:

$$(|\psi\rangle)^\dagger = \langle\psi|$$
$$(\langle\psi|)^\dagger = |\psi\rangle$$

**Propiedades de la operación $\dagger$:**
Al aplicar la operación daga sobre productos de matrices o vectores, el orden de los elementos se invierte. Para operadores $A$ y $B$, y escalares complejos $c$:
* $(cA)^\dagger = c^* A^\dagger$
* $(A B)^\dagger = B^\dagger A^\dagger$
* $(A |\psi\rangle)^\dagger = \langle\psi| A^\dagger$

Esta operación es clave para definir operadores Hermitianos y matrices unitarias, los cuales son los bloques constructores de la evolución de sistemas cuánticos.

---
### Referencias bibliográficas

* Shankar, R. (1994). *Principles of Quantum Mechanics* (2nd ed.). Springer. (Capítulo 1: Mathematical Introduction).


# 0.5 Geometría

La geometría de los espacios cuánticos se deriva de las propiedades del producto interno. A diferencia del espacio euclidiano clásico, la geometría cuántica abarca dimensiones complejas.

Para un sistema de dos niveles (un solo qubit), la restricción de normalización ($|\alpha|^2 + |\beta|^2 = 1$) y el hecho de que la fase global de un estado cuántico es inobservable (no afecta las probabilidades de medición), permiten parametrizar cualquier estado puro de un qubit usando dos ángulos reales, $\theta$ y $\varphi$:

$$|\psi\rangle = \cos\left(\frac{\theta}{2}\right)|0\rangle + e^{i\varphi}\sin\left(\frac{\theta}{2}\right)|1\rangle$$

Esta parametrización da lugar a la **esfera de Bloch**, una representación geométrica tridimensional donde todo estado puro corresponde a un punto en la superficie de una esfera unitaria. En esta geometría, la ortogonalidad tiene una representación visual peculiar: dos vectores ortogonales en el espacio de Hilbert complejo (donde $\langle\phi|\psi\rangle = 0$) se representan como puntos antípodas en polos opuestos de la esfera de Bloch (por ejemplo, los estados $|0\rangle$ y $|1\rangle$).

El valor absoluto del producto interno al cuadrado, $|\langle\phi|\psi\rangle|^2$, puede interpretarse geométricamente como la medida de la superposición (overlap) o la "cercanía" entre dos estados.

---
### Referencias bibliográficas

* Bloch, F. (1946). *Nuclear Induction*. Physical Review.
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 1: Introduction and Overview).


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
### Referencias bibliográficas

* Von Neumann, J. (1932). *Mathematical Foundations of Quantum Mechanics*. Princeton University Press. (Capítulo 2: Abstract Hilbert Space).

# 0.7 Eigenvalues and eigenvectors

El análisis espectral de los operadores es el puente entre las matemáticas de los espacios vectoriales y los resultados de las mediciones cuánticas. 

Para un operador lineal $A$, un vector no nulo $|v\rangle$ es un **eigenvector** (o vector propio) si, al aplicarle el operador, el vector resultante es solo un múltiplo escalar del original:

$$A|v\rangle = \lambda|v\rangle$$

El escalar $\lambda \in \mathbb{C}$ es el **eigenvalue** (o valor propio) asociado a ese eigenvector.

En el contexto físico de una medición cuántica descrita por un observable Hermitiano, los eigenvalores representan todos los posibles resultados deterministas numéricos de la medición, y siempre son números reales. Los eigenvectores representan los estados en los cuales colapsa el sistema inmediatamente después de ser medido.

Por el teorema espectral, todo operador Hermitiano (así como todo operador unitario) es diagonalizable. Cuando una matriz es diagonalizable, existe una base de eigenvectores en la que dicha matriz toma una forma estrictamente diagonal. En esta representación específica, la diagonal de una matriz son sus eigenvalores, facilitando enormemente el cálculo de la evolución temporal y las probabilidades del sistema.

---
### Referencias bibliográficas

* Halmos, P. R. (1958). *Finite-Dimensional Vector Spaces*. Springer. (Capítulo 2: Transformations).
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 2: Linear Algebra).

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
### Referencias bibliográficas
* Dirac, P. A. M. (1958). *The Principles of Quantum Mechanics* (4th ed.). Oxford University Press.

# 0.9 The trace

La traza de una matriz cuadrada $A$, denotada como $	ext{Tr}(A)$, se define como la suma de los elementos de su diagonal principal. En notación de Dirac con respecto a una base ortonormal $\{\vert{}i\rangle\}$:

$$	ext{Tr}(A) = \sum_i \langle i\vert{}A\vert{}i\rangle$$

Una de las propiedades más importantes de la traza en la mecánica cuántica es su invarianza cíclica. Para matrices $A$, $B$ y $C$ (asumiendo que sus dimensiones permiten la multiplicación):

$$	ext{Tr}(ABC) = 	ext{Tr}(CAB) = 	ext{Tr}(BCA)$$

Esta propiedad es especialmente útil al evaluar productos externos. La traza del producto externo entre un ket y un bra resulta ser igual a su producto interno:

$$	ext{Tr}(\vert{}\psi\rangle\langle\phi\vert{}) = \langle\phi\vert{}\psi\rangle$$

La traza también es invariante bajo cambios de base (transformaciones unitarias), lo que significa que $	ext{Tr}(U A U^\dagger) = 	ext{Tr}(A)$, haciendo de la traza una herramienta independiente del sistema de coordenadas elegido.

---
### Referencias bibliográficas
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information*. Cambridge University Press. (Capítulo 2: Linear Algebra).

# 0.10 Some useful identities

El producto tensorial ($\otimes$) es la operación matemática que nos permite unir varios sistemas cuánticos aislados. Al aplicar el producto tensorial, se multiplican las dimensiones de los qubits para formar un sistema compuesto más grande. Si tenemos un espacio $\mathcal{H}_A$ de dimensión $n$ y $\mathcal{H}_B$ de dimensión $m$, el espacio compuesto $\mathcal{H}_A \otimes \mathcal{H}_B$ tiene dimensión $nm$.

Algunas identidades fundamentales para manipular operadores y estados compuestos son:

1. **Distribución escalar:** $c(A \otimes B) = (cA) \otimes B = A \otimes (cB)$
2. **Producto mixto:** Para operadores $A, B, C, D$, se cumple que:
   $$(A \otimes B)(C \otimes D) = (AC) \otimes (BD)$$
3. **Aplicación sobre estados:** Cuando operadores actúan sobre estados compuestos:
   $$(A \otimes B)(\vert{}\psi\rangle \otimes \vert{}\phi\rangle) = (A\vert{}\psi\rangle) \otimes (B\vert{}\phi\rangle)$$
4. **Adjunto y Traza del producto tensorial:**
   $$(A \otimes B)^\dagger = A^\dagger \otimes B^\dagger$$
   $$	ext{Tr}(A \otimes B) = 	ext{Tr}(A)	ext{Tr}(B)$$

---
### Referencias bibliográficas
* Kaye, P., Laflamme, R., & Mosca, M. (2007). *An Introduction to Quantum Computing*. Oxford University Press. (Capítulo 2: The Quantum Mechanics of Qubit Systems).

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
### Referencias bibliográficas
* Born, M. (1926). *Zur Quantenmechanik der Stoßvorgänge*. Zeitschrift für Physik.
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information*. Cambridge University Press. (Capítulo 2: Postulates of Quantum Mechanics).
