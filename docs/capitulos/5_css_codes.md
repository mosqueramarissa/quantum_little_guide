# Capítulo 5: Códigos CSS y la Generación de Códigos de Superficie

Una vez dominados los códigos clásicos sobre $\mathbb{F}_2$ y el formalismo de estabilizadores, nos enfrentamos al desafío de diseñar códigos cuánticos prácticos. La familia de códigos CSS (Calderbank-Shor-Steane) representa el puente fundamental entre la teoría clásica de corrección de errores y el mundo cuántico. Al especializar los códigos CSS en una topología bidimensional, obtenemos los códigos de superficie, los cuales son la arquitectura principal para la tolerancia a fallas en la mayoría de los procesadores cuánticos actuales debido a su requerimiento de conectividad estrictamente local.

## 5.1 La Construcción Calderbank-Shor-Steane (CSS)

La genialidad de la construcción CSS radica en utilizar dos códigos clásicos para corregir errores cuánticos: uno para corregir errores de bit (operadores de Pauli $X$) y otro para corregir errores de fase (operadores de Pauli $Z$). 

Supongamos que tenemos dos códigos lineales clásicos, $C_X$ y $C_Z$, definidos por sus respectivas matrices de comprobación de paridad, $H_X$ y $H_Z$. Para que estas matrices definan un código estabilizador cuántico válido, los operadores $X$ y $Z$ generados a partir de las filas de $H_X$ y $H_Z$ deben conmutar[cite: 3]. 

Como establecimos en el formalismo de Pauli, dos operadores formados por productos tensoriales de $X$ y $Z$ conmutan si y solo si coinciden (es decir, tienen soporte simultáneo) en un número **par** de qubits. Matemáticamente, esto exige que el espacio fila de $H_X$ sea ortogonal al espacio fila de $H_Z$ sobre $\mathbb{F}_2$. La condición estricta es:
$$H_X H_Z^T = 0 \pmod 2$$

Si esta condición se cumple, podemos definir el grupo estabilizador $S$ generándolo a partir de:
*   Las filas de $H_X$, donde cada $1$ se reemplaza por un operador $X$.
*   Las filas de $H_Z$, donde cada $1$ se reemplaza por un operador $Z$.

Los síndromes se extraen midiendo estos generadores. Un síndrome de $X$ (obtenido midiendo $H_Z$) detecta errores tipo $X$, y un síndrome de $Z$ (obtenido midiendo $H_X$) detecta errores tipo $Z$.

## 5.2 El Código Toroidal y los Códigos de Superficie

Mientras que un código CSS arbitrario puede requerir que interactúen qubits distantes (lo cual es físicamente prohibitivo en arquitecturas como circuitos superconductores), Alexei Kitaev propuso asociar los qubits a las celdas geométricas de una red espacial, creando memorias cuánticas topológicas[cite: 3]. 

En el **código toroidal** original (y su versión plana, el **código de superficie**), los qubits físicos se ubican en las aristas de una cuadrícula bidimensional[cite: 3]. Los estabilizadores se asocian a las características geométricas de la red:
*   **Estabilizadores de vértice (Stars / $A_v$):** Operadores $X^{\otimes 4}$ aplicados a los 4 qubits que inciden en un vértice $v$.
*   **Estabilizadores de cara (Plaquettes / $B_p$):** Operadores $Z^{\otimes 4}$ aplicados a los 4 qubits que bordean una cara o plaquette $p$[cite: 3].

La conmutatividad ($H_X H_Z^T = 0$) está garantizada geométricamente: un vértice y una cara adyacente siempre comparten exactamente dos aristas (dos qubits), por lo que el número de intersecciones es par y los operadores $A_v$ y $B_p$ conmutan.

### 5.2.1 Interpretación Homológica de los Operadores Lógicos

La protección de la información en estos códigos es de naturaleza topológica[cite: 3]. Los estados lógicos no se almacenan en qubits individuales, sino en propiedades globales de la red. 
Los operadores lógicos $\bar{X}$ y $\bar{Z}$ corresponden a cadenas de operadores $X$ y $Z$ que atraviesan la cuadrícula de un extremo al otro (o envuelven el toro). Estas son cadenas de operadores que conmutan con todos los estabilizadores (ya que tienen un número par de intersecciones o no se cruzan), pero que no pueden ser generadas por el producto de estabilizadores individuales.

En términos de homología de grafos:
*   Los estabilizadores representan fronteras triviales (ciclos contraíbles)[cite: 3].
*   Los operadores lógicos representan ciclos no triviales que envuelven la topología del colector[cite: 3].

### 5.2.2 Detección Local de Errores

Por qué los errores locales son detectados localmente es una consecuencia directa de esta topología[cite: 3]. Cuando ocurre un error (por ejemplo, una cadena de errores $X$ en qubits adyacentes), los estabilizadores en los extremos de la cadena anticonmutarán con el error, provocando un resultado de medición de $-1$ (un "defecto" o "anyón")[cite: 3]. Los estabilizadores a lo largo de la cadena (en el medio) compartirán dos qubits con el error, por lo que conmutarán y arrojarán $+1$. Por lo tanto, sin importar qué tan larga sea la cadena de errores, el síndrome solo "ilumina" los extremos de la cadena, permitiendo que un algoritmo de emparejamiento (como Minimum Weight Perfect Matching) deduzca la corrección más probable emparejando los defectos observados[cite: 3].

La distancia del código $d$ es la longitud de la cadena lógica más corta que atraviesa la cuadrícula, que escala linealmente con la longitud $L$ del parche geométrico ($d = L$).

---

## Práctica Interactiva: Construcción Matricial de un Código CSS y Generación de Parámetros

El primer paso explícito de tu trabajo de agosto requiere generar un código de superficie pequeño y reproducir sus parámetros básicos. En lugar de usar topologías complejas inmediatamente, demostraremos el núcleo del diseño CSS verificando computacionalmente la condición de ortogonalidad $H_X H_Z^T = 0 \pmod 2$.

El siguiente código implementa el código CSS más pequeño posible capaz de corregir un error arbitrario: el código de Steane $\llbracket 7, 1, 3 \rrbracket$. Este código se deriva del código clásico de Hamming $[7,4,3]$ utilizado en el capítulo anterior. 

Ejecuta este *script* en tu entorno para generar las matrices y comprobar matemáticamente por qué este código admite una estructura de grupo estabilizador válido.

```python
import numpy as np

# Para el código de Steane [[7, 1, 3]], utilizamos la matriz de paridad
# del código de Hamming [7, 4, 3] tanto para los errores X como para los Z.
# Esto asegura que el código resultante sea simétrico.

H_hamming = np.array([
    [1, 0, 0, 1, 0, 1, 1],
    [0, 1, 0, 1, 1, 0, 1],
    [0, 0, 1, 0, 1, 1, 1]
])

# En la construcción CSS, H_X y H_Z provienen de códigos clásicos
H_X = H_hamming
H_Z = H_hamming

print("=== Parámetros Básicos del Código CSS ===")
n = H_X.shape[1]
k_X = n - np.linalg.matrix_rank(H_X) # Qubits de información del código clásico C_X
k_Z = n - np.linalg.matrix_rank(H_Z) # Qubits de información del código clásico C_Z
k_logical = k_X + k_Z - n            # Dimensión cuántica del código CSS

print(f"Número de qubits físicos (n): {n}")
print(f"Número de qubits lógicos (k): {k_logical}")
print(f"Número de generadores X: {H_X.shape[0]}")
print(f"Número de generadores Z: {H_Z.shape[0]}")

# Verificación de la condición fundamental CSS (Ortogonalidad)
# Si H_X * H_Z^T (mod 2) no es cero, los estabilizadores X y Z no conmutarían.
conmutatividad_check = np.dot(H_X, H_Z.T) % 2

print("\n=== Verificación de Conmutatividad (H_X * H_Z^T mod 2) ===")
print(conmutatividad_check)

if np.all(conmutatividad_check == 0):
    print("\n[ÉXITO] La matriz resultante es nula. Los estabilizadores X y Z conmutan.")
    print("El código cuántico CSS está bien definido matemáticamente.")
else:
    print("\n[ERROR] Las matrices no son ortogonales. El subgrupo no es abeliano.")

# Visualización de un estabilizador
def matriz_a_pauli(fila, tipo):
    return "".join([tipo if bit == 1 else "I" for bit in fila])

print("\n=== Generadores del Grupo Estabilizador ===")
for i, fila in enumerate(H_X):
    print(f"Generador X_{i+1}: {matriz_a_pauli(fila, 'X')}")
for i, fila in enumerate(H_Z):
    print(f"Generador Z_{i+1}: {matriz_a_pauli(fila, 'Z')}")

```

### Referencias bibliográficas
Dennis, E., Kitaev, A., Landahl, A., & Preskill, J. (2002). "Topological quantum memory", Journal of Mathematical Physics, 43, 4452–4505.
Gottesman, D. (2010). "An introduction to quantum error correction and fault-tolerant quantum computation", en Quantum Information Science and Its Contributions to Mathematics.
Kitaev, A. Y. (2003). "Fault-tolerant quantum computation by anyons", Annals of Physics, 303, 2–30.
Nielsen, M. A., & Chuang, I. L. (2010). Quantum Computation and Quantum Information, Cambridge University Press.