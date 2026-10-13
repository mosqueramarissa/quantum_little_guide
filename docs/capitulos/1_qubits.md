# Capítulo 1: Qubits y Entrelazamiento

## 1.1 El Qubit y la Esfera de Bloch

En la computación clásica, la unidad fundamental de información es el bit, el cual posee un estado determinista y mutuamente excluyente: $0$ o $1$. En contraste, la mecánica cuántica postula que el estado de un sistema físico aislado se describe completamente mediante un vector de estado en un espacio de Hilbert complejo. 

Para la computación cuántica, el sistema más simple es el **qubit** (bit cuántico), el cual reside en un espacio de Hilbert bidimensional, denotado como $\mathcal{H} \cong \mathbb{C}^2$.

### La Base Computacional y la Superposición

Definimos una base ortonormal para este espacio, conocida como la **base computacional**, denotada por los kets $|0\rangle$ y $|1\rangle$. En su representación vectorial clásica, se escriben como:

$$|0\rangle = \begin{pmatrix} 1 \\ 0 \end{pmatrix}, \quad |1\rangle = \begin{pmatrix} 0 \\ 1 \end{pmatrix}$$

A diferencia de un bit clásico, el principio de superposición establece que un qubit puede existir en cualquier combinación lineal de estos estados base. Por lo tanto, el estado general de un qubit, $|\psi\rangle$, se expresa como:

$$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle = \begin{pmatrix} \alpha \\ \beta \end{pmatrix}$$

Donde $\alpha, \beta \in \mathbb{C}$ son las **amplitudes de probabilidad**. 

### La Condición de Normalización

Como vimos en la Parte I de este libro, la probabilidad de medir un estado cuántico debe conservar la unidad. Según la regla de Born, la probabilidad de medir el estado $|0\rangle$ es $|\alpha|^2$ y la de medir $|1\rangle$ es $|\beta|^2$. Esto nos impone una restricción geométrica estricta, la condición de normalización:

$$|\alpha|^2 + |\beta|^2 = 1$$

Esta ecuación nos indica que el vector de estado $|\psi\rangle$ es un vector unitario, confinado a la superficie de una esfera en el espacio complejo bidimensional.

### Reducción de Grados de Libertad: Fase Global vs. Fase Relativa

A primera vista, los números complejos $\alpha$ y $\beta$ requieren cuatro parámetros reales para definirse ($a + bi$ y $c + di$). Sin embargo, podemos reducir estos grados de libertad basándonos en la física del sistema:

1. **Normalización:** La restricción $|\alpha|^2 + |\beta|^2 = 1$ elimina un grado de libertad (dejando tres).
2. **Fase Global:** Podemos reescribir las amplitudes en su forma polar: $\alpha = r_0 e^{i\gamma_0}$ y $\beta = r_1 e^{i\gamma_1}$. Factorizando la fase del primer término:
   $$|\psi\rangle = e^{i\gamma_0} \left( r_0 |0\rangle + r_1 e^{i(\gamma_1 - \gamma_0)} |1\rangle \right)$$
   En la mecánica cuántica, una **fase global** ($e^{i\gamma_0}$) multiplicada por todo el vector de estado no tiene ningún efecto observable en las probabilidades de medición ni en los valores esperados. Dos estados que difieren solo por una fase global representan el mismo estado físico. Por lo tanto, podemos ignorarla.

Esto nos deja con solo dos parámetros reales físicamente significativos. Definimos $r_0 = \cos(\theta/2)$, $r_1 = \sin(\theta/2)$ (para garantizar la normalización de forma natural usando la identidad trigonométrica $\cos^2 x + \sin^2 x = 1$), y la **fase relativa** $\phi = \gamma_1 - \gamma_0$.

### La Representación Geométrica: La Esfera de Bloch

Con esta parametrización, llegamos a la ecuación canónica del estado de un solo qubit:

$$|\psi\rangle = \cos\left(\frac{\theta}{2}\right) |0\rangle + e^{i\phi} \sin\left(\frac{\theta}{2}\right) |1\rangle$$

Donde:
*   $0 \leq \theta \leq \pi$ determina las probabilidades de medición (es el ángulo polar).
*   $0 \leq \phi < 2\pi$ determina la fase relativa (es el ángulo azimutal).

Esta formulación mapea perfectamente cualquier estado puro de un qubit a un punto en la superficie de una esfera unitaria tridimensional real, conocida como la **Esfera de Bloch**.

*   El Polo Norte ($\theta = 0$) corresponde al estado $|0\rangle$.
*   El Polo Sur ($\theta = \pi$) corresponde al estado $|1\rangle$.
*   Cualquier punto en el ecuador ($\theta = \pi/2$) representa una superposición equitativa, como el estado $|+\rangle = \frac{1}{\sqrt{2}}(|0\rangle + |1\rangle)$ cuando $\phi = 0$.

---

### 💻 Actividad Interactiva: Manipulación de la Esfera de Bloch

**Objetivo:** Evitar la abstracción puramente algebraica y visualizar el impacto de la fase relativa.

**Instrucciones para el Notebook (`12_01_bloch_interactive.ipynb`):**
1. Ejecuta la celda de inicialización para cargar el entorno gráfico.
2. Utiliza el primer control deslizante para modificar el ángulo $\theta$. Observa cómo el vector de estado transita entre el Polo Norte ($|0\rangle$) y el Polo Sur ($|1\rangle$). Presta atención a las barras de probabilidad en el panel derecho.
3. Posiciona $\theta$ en $\pi/2$ (el ecuador). Ahora, utiliza el segundo control deslizante para modificar el ángulo $\phi$ de $0$ a $2\pi$. 
4. **Análisis:** Nota que al cambiar $\phi$ mientras el vector está en el ecuador, el vector gira, pero las barras de probabilidad de medir $0$ o $1$ *no cambian*. Esta es la visualización directa de por qué la fase relativa almacena información crucial en los algoritmos cuánticos sin alterar las probabilidades inmediatas de la base computacional.

## 12.2 Sistemas Multiqubit y los Estados de Bell

Hasta ahora, hemos analizado el estado de un único qubit. Sin embargo, el verdadero poder de la computación cuántica surge cuando combinamos múltiples sistemas cuánticos. Matemáticamente, el espacio de estados de un sistema compuesto se construye mediante el **producto tensorial** de los espacios de Hilbert de los sistemas individuales.

Si tenemos un qubit $A$ en el espacio $\mathcal{H}_A$ y un qubit $B$ en el espacio $\mathcal{H}_B$, el sistema combinado reside en el espacio $\mathcal{H}_{AB} = \mathcal{H}_A \otimes \mathcal{H}_B$, el cual tiene dimensión $2 \times 2 = 4$.

### La Base Computacional de Dos Qubits

La base ortonormal para este sistema compuesto se forma tomando el producto tensorial de los vectores base individuales. Para dos qubits, la base computacional es:

$$
\{\vert{}00\rangle, \vert{}01\rangle, \vert{}10\rangle, \vert{}11\rangle\}
$$

Donde la notación $\vert{}xy\rangle$ es una abreviatura de $\vert{}x\rangle \otimes \vert{}y\rangle$. El estado general de un sistema de dos qubits es una superposición de estos cuatro estados base:

$$
\vert{}\psi\rangle = \alpha_{00}\vert{}00\rangle + \alpha_{01}\vert{}01\rangle + \alpha_{10}\vert{}10\rangle + \alpha_{11}\vert{}11\rangle
$$

Sujeto a la condición de normalización $\sum_{i,j} \vert{}\alpha_{ij}\vert{}^2 = 1$.

### Estados Separables vs. Estados Entrelazados

Un estado de dos qubits se denomina **separable** si puede escribirse como el producto tensorial de los estados de los qubits individuales. Es decir, si existen $\vert{}\psi_A\rangle$ y $\vert{}\psi_B\rangle$ tales que:

$$
\vert{}\psi_{AB}\rangle = \vert{}\psi_A\rangle \otimes \vert{}\psi_B\rangle
$$

Por ejemplo, el estado $\frac{1}{2}(\vert{}00\rangle + \vert{}01\rangle + \vert{}10\rangle + \vert{}11\rangle)$ es separable porque se puede factorizar como $\frac{1}{\sqrt{2}}(\vert{}0\rangle + \vert{}1\rangle) \otimes \frac{1}{\sqrt{2}}(\vert{}0\rangle + \vert{}1\rangle)$.

Sin embargo, existen estados en el espacio $\mathcal{H}_{AB}$ que **no pueden** factorizarse de esta manera. A estos estados se les conoce como **entrelazados**. El entrelazamiento es una propiedad netamente cuántica que indica que los subsistemas no poseen estados individuales bien definidos; solo el sistema global tiene un estado definido.

### Los Estados de Bell

El ejemplo canónico de entrelazamiento bipartito son los cuatro **estados de Bell** (o pares EPR), que representan estados máximamente entrelazados de dos qubits:

$$
\vert{}\Phi^+\rangle = \frac{1}{\sqrt{2}} (\vert{}00\rangle + \vert{}11\rangle)
$$
$$
\vert{}\Phi^-\rangle = \frac{1}{\sqrt{2}} (\vert{}00\rangle - \vert{}11\rangle)
$$
$$
\vert{}\Psi^+\rangle = \frac{1}{\sqrt{2}} (\vert{}01\rangle + \vert{}10\rangle)
$$
$$
\vert{}\Psi^-\rangle = \frac{1}{\sqrt{2}} (\vert{}01\rangle - \vert{}10\rangle)
$$

Tomemos como ejemplo $\vert{}\Phi^+\rangle$. No importa cómo intentes definir $\vert{}\psi_A\rangle = a\vert{}0\rangle + b\vert{}1\rangle$ y $\vert{}\psi_B\rangle = c\vert{}0\rangle + d\vert{}1\rangle$, es algebraicamente imposible que su producto $(ac\vert{}00\rangle + ad\vert{}01\rangle + bc\vert{}10\rangle + bd\vert{}11\rangle)$ iguale a $\frac{1}{\sqrt{2}}\vert{}00\rangle + 0\vert{}01\rangle + 0\vert{}10\rangle + \frac{1}{\sqrt{2}}\vert{}11\rangle$, ya que requeriría que $ad = 0$ y $bc = 0$ sin que $ac$ ni $bd$ sean cero, lo cual es una contradicción.

---

## 12.3 Correlaciones No Clásicas y Medición

La consecuencia física de la inseparabilidad algebraica se manifiesta al realizar mediciones sobre el sistema. 

Consideremos el estado de Bell $\vert{}\Phi^+\rangle = \frac{1}{\sqrt{2}} (\vert{}00\rangle + \vert{}11\rangle)$. Supongamos que los qubits $A$ y $B$ se separan espacialmente y un observador mide el qubit $A$ en la base computacional.

Antes de la medición, la probabilidad de medir $\vert{}0\rangle$ en el qubit $A$ es exactamente del $50\%$ ($\vert{}\frac{1}{\sqrt{2}}\vert{}^2$), al igual que la probabilidad de medir $\vert{}1\rangle$. El resultado es intrínsecamente aleatorio.

### El Colapso Correlacionado

Si la medición del qubit $A$ arroja el resultado $0$, el postulado de la medición dicta que el estado global colapsa, proyectándose sobre el subespacio compatible con ese resultado. El término $\vert{}11\rangle$ desaparece, y el estado post-medición del sistema se convierte (tras normalizar) en:

$$
\vert{}\psi'_{AB}\rangle = \vert{}00\rangle
$$

En este instante, el estado del qubit $B$ queda perfectamente determinado como $\vert{}0\rangle$. Si un segundo observador mide el qubit $B$, obtendrá el resultado $0$ con un $100\%$ de certeza. 

Por el contrario, si la medición del qubit $A$ hubiera arrojado el resultado $1$, el estado global colapsaría a $\vert{}11\rangle$, garantizando que la posterior medición de $B$ arroje $1$.

### Superando los Límites Clásicos

Estas correlaciones son "más fuertes" que cualquier correlación clásica posible. A diferencia de un par de calcetines de distinto color guardados en cajas separadas (donde el color de cada uno ya estaba determinado desde el principio, simplemente lo desconocíamos), en la mecánica cuántica el estado individual de los qubits **no existía** antes de la medición. El teorema de Bell demuestra matemáticamente que ninguna teoría de "variables ocultas locales" (la idea de que los qubits llevaban instrucciones secretas previas) puede reproducir las predicciones estadísticas de la mecánica cuántica para estados entrelazados medidos en diferentes bases.

Esta característica es la base para protocolos avanzados como la teletransportación cuántica y la criptografía cuántica, y será fundamental cuando estudiemos cómo la medición de síndromes afecta a un bloque de qubits en los códigos correctores de errores.

### 💻 Actividad Interactiva: Medición de un Estado de Bell

**Objetivo:** Simular programáticamente el colapso del estado en sistemas multiqubit y validar las probabilidades marginales y condicionales.

**Instrucciones para el Notebook (`12_02_bell_correlations.ipynb`):**

1. Define el vector de estado para $\vert{}\Phi^+\rangle$ usando arreglos de NumPy.
2. Construye los operadores de proyección para el primer qubit: $P_0 = \vert{}0\rangle\langle0\vert{} \otimes I$ y $P_1 = \vert{}1\rangle\langle1\vert{} \otimes I$.
3. Aplica computacionalmente el proyector $P_0$ sobre el estado $\vert{}\Phi^+\rangle$ y normaliza el resultado. Observa cómo el vector resultante corresponde estrictamente al estado $\vert{}00\rangle$.
4. **Análisis algorítmico:** Modifica el código para aplicar una compuerta de rotación local únicamente al qubit $A$ antes de la medición. Recalcula las probabilidades conjuntas de medir $(0,0), (0,1), (1,0)$ y $(1,1)$. Esto revelará cómo las operaciones locales afectan las correlaciones globales sin violar la no-comunicación más rápida que la luz.