# Capítulo 7: La Familia de Códigos qLDPC y el Producto de Hipergrafos

Hasta ahora hemos estudiado el código de superficie, el cual es elegante por su topología plana y conectividad estrictamente local. Sin embargo, sufre de una limitación asintótica severa: para codificar un solo qubit lógico con mayor protección (aumentar la distancia $d$), necesitamos crecer la cuadrícula cuadráticamente, es decir, el número de qubits físicos $n$ escala como $O(d^2)$. En términos matemáticos, la tasa de codificación $k/n$ tiende a cero a medida que el sistema crece.

Para construir computadoras cuánticas verdaderamente escalables sin requerir millones de qubits físicos, la teoría de la información cuántica ha centrado sus esfuerzos en los **códigos cuánticos de comprobación de paridad de baja densidad (qLDPC)**. Esta familia busca lograr una tasa de codificación constante ($k/n \to c > 0$) y una distancia que escale favorablemente, manteniendo un límite superior en el número de comprobaciones en las que participa cualquier qubit individual (grado acotado en el Grafo de Tanner)[cite: 2, 3].

## 7.1 El Reto de la Conmutatividad en qLDPC

En la teoría de códigos clásicos, la adopción de matrices LDPC aleatorias o pseudoaleatorias revolucionó las telecomunicaciones. Sin embargo, en el dominio cuántico, no podemos simplemente tomar dos matrices LDPC clásicas aleatorias $H_X$ y $H_Z$. 

Como vimos en el Capítulo 16, la construcción CSS impone una restricción rígida: el espacio fila de $H_X$ y el espacio fila de $H_Z$ deben ser ortogonales sobre el campo finito $\mathbb{F}_2$. Es decir, $H_X H_Z^T = 0 \pmod 2$. Encontrar matrices dispersas (LDPC) que satisfagan simultáneamente una alta tasa de codificación, una gran distancia mínima y esta estricta condición de ortogonalidad geométrica es un problema combinatorio formidable.

Para resolver esto, utilizamos construcciones algebraicas estructuradas. Las dos familias de qLDPC más relevantes para implementaciones a pequeña escala son los códigos tipo *Bicycle* y el Producto de Hipergrafos[cite: 2].

## 7.2 Códigos tipo *Bicycle* y *Generalized Bicycle*

Una forma heurística de garantizar la ortogonalidad es utilizar matrices circulantes (matrices cuadradas donde cada fila es un desplazamiento cíclico de la anterior). 

Supongamos que tenemos dos matrices circulantes que conmutan, $A$ y $B$, con elementos en $\mathbb{F}_2$. Podemos definir las matrices de comprobación de paridad CSS de la siguiente manera:
$$H_X = [A \mid B]$$
$$H_Z = [B^T \mid A^T]$$

Demostremos matemáticamente la conmutatividad:
$$H_X H_Z^T = [A \mid B] \begin{bmatrix} B \\ A \end{bmatrix} = AB + BA \pmod 2$$
Dado que $A$ y $B$ son circulantes, conmutan ($AB = BA$). Y puesto que estamos en aritmética módulo 2, la suma de dos elementos idénticos es cero ($X \oplus X = 0$). Por lo tanto:
$$AB + BA = 2AB \equiv 0 \pmod 2$$
Esta estructura simple permite encontrar códigos cuánticos funcionales mediante la búsqueda de polinomios cíclicos apropiados, generando pequeños códigos *bicycle* útiles para experimentos tempranos en simuladores de ruido[cite: 2].

## 7.3 La Construcción del Producto de Hipergrafos

El avance más trascendental en la teoría qLDPC moderna fue la invención de la construcción del producto de hipergrafos por Tillich y Zémor. Este método es brillante porque proporciona una receta sistemática para tomar **cualquier par de códigos clásicos** y combinarlos en un código cuántico CSS válido[cite: 3]. Si los códigos clásicos originales son LDPC, el código cuántico resultante está garantizado de ser qLDPC[cite: 3].

### 7.3.1 Definición de las Matrices

Sean dos códigos clásicos $C_1$ y $C_2$ (no necesariamente idénticos ni cuadrados) definidos por sus matrices de comprobación de paridad:
*   $H_1$: matriz de tamaño $r_1 \times n_1$
*   $H_2$: matriz de tamaño $r_2 \times n_2$

El producto de hipergrafos define un código cuántico en un espacio de Hilbert de $n = n_1 n_2 + r_1 r_2$ qubits físicos. Las matrices de paridad del código CSS resultante se construyen utilizando el producto tensorial de Kronecker ($\otimes$) y matrices identidad ($I$):

$$H_X = [ H_1 \otimes I_{n_2} \mid I_{r_1} \otimes H_2^T ]$$
$$H_Z = [ I_{n_1} \otimes H_2 \mid H_1^T \otimes I_{r_2} ]$$

### 7.3.2 Demostración Formal de Ortogonalidad CSS

Para verificar que esta construcción engendra un código cuántico válido, debemos demostrar rigurosamente que $H_X H_Z^T = 0 \pmod 2$. 

Recordemos dos propiedades fundamentales del producto de Kronecker para matrices:
1.  **Transpuesta:** $(A \otimes B)^T = A^T \otimes B^T$
2.  **Producto mixto:** $(A \otimes B)(C \otimes D) = (AC) \otimes (BD)$ (siempre que las dimensiones internas coincidan).

Procedemos a evaluar el producto $H_X H_Z^T$:

1.  Primero, transponemos $H_Z$:
    $$H_Z^T = \begin{bmatrix} (I_{n_1} \otimes H_2)^T \\ (H_1^T \otimes I_{r_2})^T \end{bmatrix} = \begin{bmatrix} I_{n_1}^T \otimes H_2^T \\ (H_1^T)^T \otimes I_{r_2}^T \end{bmatrix} = \begin{bmatrix} I_{n_1} \otimes H_2^T \\ H_1 \otimes I_{r_2} \end{bmatrix}$$

2.  Ahora multiplicamos $H_X$ por $H_Z^T$ realizando la multiplicación de matrices por bloques:
    $$H_X H_Z^T = [ H_1 \otimes I_{n_2} \mid I_{r_1} \otimes H_2^T ] \begin{bmatrix} I_{n_1} \otimes H_2^T \\ H_1 \otimes I_{r_2} \end{bmatrix}$$
    $$H_X H_Z^T = (H_1 \otimes I_{n_2})(I_{n_1} \otimes H_2^T) + (I_{r_1} \otimes H_2^T)(H_1 \otimes I_{r_2})$$

3.  Aplicamos la propiedad del producto mixto término a término:
    *   Primer término: $(H_1 I_{n_1}) \otimes (I_{n_2} H_2^T) = H_1 \otimes H_2^T$
    *   Segundo término: $(I_{r_1} H_1) \otimes (H_2^T I_{r_2}) = H_1 \otimes H_2^T$

4.  Sumamos los términos resultantes:
    $$H_X H_Z^T = (H_1 \otimes H_2^T) + (H_1 \otimes H_2^T)$$

Dado que estamos operando sobre el campo finito $\mathbb{F}_2$, la suma de cualquier matriz consigo misma es la matriz nula:
$$(H_1 \otimes H_2^T) \oplus (H_1 \otimes H_2^T) \equiv 0 \pmod 2$$

Esta demostración prueba que el producto de hipergrafos de Tillich y Zémor garantiza algorítmicamente la condición de conmutatividad necesaria para el formalismo estabilizador, sin importar qué matrices $H_1$ y $H_2$ se hayan elegido originalmente[cite: 3].

## 7.4 Desafíos Físicos: qLDPC vs Topología del Hardware

Aunque el producto de hipergrafos resuelve el problema de diseño lógico, introduce un desafío formidable en la física experimental. Trabajos recientes demuestran que ciertos códigos qLDPC de alta tasa tienen grafos de Tanner con requerimientos de conectividad no planos (por ejemplo, grado seis o interacciones de largo alcance)[cite: 2]. 

Esta desconexión entre la topología del Grafo de Tanner (incidencia lógica) y el grafo de acoplamiento del hardware (físico, a menudo restringido a conectividad de grado 3 o 4 en sistemas superconductores de 2D) fuerza el uso agresivo de compuertas SWAP[cite: 2]. Como veremos, un Grafo de Tanner con grosor dos podría descomponerse en subgrafos planares, sugiriendo arquitecturas de hardware multicapa para aliviar el enrutamiento[cite: 2], lo que representa la base de las heurísticas de colocación de qubits que compararemos.

---

## Práctica Interactiva: Generador del Producto de Hipergrafos

Para materializar las metas de septiembre[cite: 2], implementaremos un módulo para generar códigos pequeños mediante el producto de hipergrafos. El siguiente *script* toma dos códigos de repetición simples clásicos y construye el código cuántico resultante, verificando numéricamente la demostración matricial que acabamos de realizar.

Copia este código y experimenta cambiando las matrices $H_1$ y $H_2$.

```python
import numpy as np

def hipergrafo_producto(H1, H2):
    """
    Construye las matrices CSS (H_X, H_Z) usando el Producto de Hipergrafos
    a partir de dos matrices clásicas H1 y H2.
    """
    r1, n1 = H1.shape
    r2, n2 = H2.shape
    
    # Matrices identidad necesarias
    I_n1 = np.eye(n1, dtype=int)
    I_n2 = np.eye(n2, dtype=int)
    I_r1 = np.eye(r1, dtype=int)
    I_r2 = np.eye(r2, dtype=int)
    
    # Construcción de H_X = [ H1 ⊗ I_n2 | I_r1 ⊗ H2^T ]
    H_X_left = np.kron(H1, I_n2)
    H_X_right = np.kron(I_r1, H2.T)
    H_X = np.hstack((H_X_left, H_X_right)) % 2
    
    # Construcción de H_Z = [ I_n1 ⊗ H2 | H1^T ⊗ I_r2 ]
    H_Z_left = np.kron(I_n1, H2)
    H_Z_right = np.kron(H1.T, I_r2)
    H_Z = np.hstack((H_Z_left, H_Z_right)) % 2
    
    return H_X, H_Z

# --- Ejemplo de Uso ---
# Usaremos un código clásico de repetición [3, 1, 3] para H1 y H2
H_rep = np.array([
    [1, 1, 0],
    [0, 1, 1]
])

print("Matriz clásica H1 y H2 (Código de repetición):")
print(H_rep)
print("-" * 40)

H_X, H_Z = hipergrafo_producto(H_rep, H_rep)

n_qubits = H_X.shape[1]
print(f"Número total de qubits físicos del nuevo código: {n_qubits}")
print(f"Dimensión de H_X: {H_X.shape}")
print(f"Dimensión de H_Z: {H_Z.shape}")
print("-" * 40)

# Verificación de ortogonalidad H_X * H_Z^T = 0 mod 2
conmutatividad = np.dot(H_X, H_Z.T) % 2
es_valido = np.all(conmutatividad == 0)

if es_valido:
    print("[ÉXITO] H_X * H_Z^T (mod 2) = 0.")
    print("La construcción matemática generó un código CSS válido.")
else:
    print("[ERROR] Las matrices no conmutan.")
```

### Referencias bibliográficas
Tillich, J.-P., & Zémor, G. (2014). "Quantum LDPC codes with positive rate and minimum distance proportional to the square root of the blocklength", IEEE Transactions on Information Theory, 60, 1193–1202.
Breuckmann, N. P., & Eberhardt, J. N. (2021). "Quantum low-density parity-check codes", PRX Quantum, 2, 040101.