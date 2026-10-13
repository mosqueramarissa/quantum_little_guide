# Capítulo 3: Códigos Clásicos y Álgebra Lineal Binaria

La corrección de errores cuánticos, en particular la familia de códigos CSS (Calderbank-Shor-Steane), se construye directamente sobre la teoría de códigos clásicos[cite: 3]. Para comprender cómo proteger un qubit, primero debemos dominar matemáticamente cómo proteger un bit clásico utilizando redundancia estructurada. Esto requiere abandonar el cuerpo de los números reales o complejos y operar estrictamente sobre el campo finito de dos elementos: $\mathbb{F}_2$ (aritmética módulo 2)[cite: 2].

## 3.1 El Campo Finito $\mathbb{F}_2$ y el Espacio Vectorial $\mathbb{F}_2^n$

En álgebra lineal binaria, los escalares solo pueden tomar los valores $0$ o $1$. La suma se define como la operación lógica XOR ($\oplus$) y la multiplicación como la operación lógica AND ($\land$):

*   $0 \oplus 0 = 0$
*   $1 \oplus 0 = 1$
*   $1 \oplus 1 = 0$ (La característica fundamental de $\mathbb{F}_2$)

Un mensaje de $n$ bits se representa como un vector columna $\vec{v} \in \mathbb{F}_2^n$. El espacio de todos los mensajes posibles tiene dimensión $n$ y contiene $2^n$ vectores.

## 3.2 Matrices Generadoras ($G$) y de Comprobación de Paridad ($H$)

Un código lineal clásico $C$ es simplemente un subespacio vectorial de $\mathbb{F}_2^n$ de dimensión $k$ (donde $k < n$). Los vectores que pertenecen a este subespacio se denominan **palabras de código** (*codewords*).

Podemos definir este subespacio de dos maneras complementarias:

**1. Matriz Generadora ($G$):**
Es una matriz de dimensiones $n \times k$ cuyas columnas forman una base para el subespacio $C$. Toma un mensaje original $\vec{u} \in \mathbb{F}_2^k$ y lo mapea a una palabra de código $\vec{x} \in \mathbb{F}_2^n$:
$$\vec{x} = G \vec{u} \pmod 2$$

**2. Matriz de Comprobación de Paridad ($H$):**
Es una matriz de dimensiones $(n-k) \times n$. Define el código $C$ como su espacio nulo (o núcleo). Un vector $\vec{x}$ es una palabra de código válida si y solo si satisface:
$$H \vec{x} = \vec{0} \pmod 2$$

La relación fundamental entre ambas matrices, dado que el espacio fila de $G$ debe ser ortogonal al espacio fila de $H$, es:
$$H G^T = 0 \pmod 2$$

## 3.3 Distancia de Hamming y Síndromes

El **peso de Hamming** $\text{wt}(\vec{x})$ de un vector es el número de coordenadas distintas de cero (cuántos '1's tiene).
La **distancia de Hamming** $d(\vec{x}, \vec{y})$ entre dos vectores es el peso de su diferencia: $\text{wt}(\vec{x} \oplus \vec{y})$. Representa el número de bits en los que difieren.

La distancia mínima $d$ de un código lineal $C$ es el peso mínimo de una palabra de código no nula:
$$d = \min_{\vec{x} \in C, \vec{x} \neq \vec{0}} \text{wt}(\vec{x})$$
Un código puede detectar hasta $d-1$ errores y corregir hasta $\lfloor \frac{d-1}{2} \rfloor$ errores.

Si se transmite una palabra de código $\vec{x}$ pero ocurre un vector de error $\vec{e}$ (donde los '1's en $\vec{e}$ indican qué bits se voltearon), el vector recibido es $\vec{r} = \vec{x} \oplus \vec{e}$.

Al multiplicar el vector recibido por $H$, obtenemos el **síndrome** $\vec{s}$:
$$\vec{s} = H \vec{r} = H (\vec{x} \oplus \vec{e}) = H\vec{x} \oplus H\vec{e}$$
Como $H\vec{x} = 0$ por definición, tenemos que:
$$\vec{s} = H \vec{e} \pmod 2$$
El síndrome depende *exclusivamente* del error, no del mensaje transmitido. Esta es la base conceptual de la extracción de síndromes sin destruir información, que aplicaremos en mecánica cuántica en el siguiente capítulo[cite: 2, 3].

---

## Práctica Interactiva: Implementación de Álgebra Lineal sobre $\mathbb{F}_2$

Para evitar la lectura pasiva y asentar las bases del desarrollo de tu paquete computacional[cite: 2], implementaremos estas operaciones en código. El siguiente bloque te permite verificar matemáticamente la relación $HG^T = 0$ y calcular el síndrome de un vector con error.

Copia este código en tu entorno de desarrollo o notebook y experimenta cambiando el vector de error `e` para ver cómo reacciona el síndrome.

```python
import numpy as np

# Definimos el código clásico [7, 4, 3] de Hamming
# Matriz Generadora G (7x4)
G = np.array([
    [1, 1, 0, 1],
    [1, 0, 1, 1],
    [1, 0, 0, 0],
    [0, 1, 1, 1],
    [0, 1, 0, 0],
    [0, 0, 1, 0],
    [0, 0, 0, 1]
])

# Matriz de Comprobación de Paridad H (3x7)
H = np.array([
    [1, 0, 1, 0, 1, 0, 1],
    [0, 1, 1, 0, 0, 1, 1],
    [0, 0, 0, 1, 1, 1, 1]
])

# 1. Verificación de Ortogonalidad: H * G^T (mod 2) debe ser la matriz nula
ortogonalidad = np.dot(H, G) % 2
print("Comprobación H * G (mod 2) = 0:")
print(ortogonalidad)
print("-" * 30)

# 2. Codificación de un mensaje
u = np.array([1, 0, 1, 1]) # Mensaje de 4 bits
x = np.dot(G, u) % 2       # Palabra de código transmitida
print(f"Mensaje u: {u}")
print(f"Palabra de código x: {x}")

# 3. Introducción de un error e (modificamos el tercer bit)
e = np.array([0, 0, 1, 0, 0, 0, 0])
r = (x + e) % 2            # Vector recibido
print(f"Vector recibido r con error: {r}")

# 4. Cálculo del Síndrome
s = np.dot(H, r) % 2
print(f"Síndrome calculado s = H*r (mod 2): {s}")

# 5. Verificación de que el síndrome es idéntico a H*e
s_teorico = np.dot(H, e) % 2
print(f"Síndrome teórico s = H*e (mod 2): {s_teorico}")