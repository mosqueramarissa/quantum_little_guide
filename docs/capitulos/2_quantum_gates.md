# Capítulo 2: Compuertas Cuánticas y la Fragilidad de la Información

En el capítulo anterior, establecimos que el estado de un sistema cuántico cerrado se describe mediante un vector en un espacio de Hilbert. Sin embargo, la computación requiere que *procesemos* esta información. Por los postulados de la mecánica cuántica, la evolución temporal de un sistema cerrado debe ser descrita por un operador unitario $U$, donde $U^\dagger U = I$. En el contexto de los circuitos, estos operadores unitarios son nuestras **compuertas cuánticas**.

## 2.1 El Grupo de Clifford y Compuertas Fundamentales

Para manipular un qubit, utilizamos matrices unitarias de $2 \times 2$. Las transformaciones más fundamentales provienen de las **matrices de Pauli**, que actúan como compuertas lógicas y, crucialmente, formarán el núcleo de nuestro análisis de errores más adelante.

Las compuertas de Pauli son:

$$
X = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix}, \quad 
Y = \begin{pmatrix} 0 & -i \\ i & 0 \end{pmatrix}, \quad 
Z = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix}
$$

*   La compuerta $X$ es el análogo cuántico de la compuerta NOT clásica: $X|0\rangle = |1\rangle$ y $X|1\rangle = |0\rangle$. Geométricamente, es una rotación de $\pi$ radianes alrededor del eje X en la esfera de Bloch.
*   La compuerta $Z$ aplica un **cambio de fase** relativo: $Z|0\rangle = |0\rangle$ y $Z|1\rangle = -|1\rangle$. Esta operación no tiene análogo clásico.

Para crear superposiciones, utilizamos la **compuerta Hadamard** ($H$), que transforma la base computacional en una base de superposición equitativa (los ejes X de la esfera de Bloch):

$$
H = \frac{1}{\sqrt{2}} \begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix} \quad \implies \quad H|0\rangle = \frac{1}{\sqrt{2}}(|0\rangle + |1\rangle) \equiv |+\rangle
$$

También definimos la **compuerta de Fase** ($S$), que aplica una rotación de $\pi/2$ alrededor del eje Z:

$$
S = \begin{pmatrix} 1 & 0 \\ 0 & i \end{pmatrix}
$$

### Operaciones de Múltiples Qubits

Para generar el entrelazamiento que discutimos en el Capítulo 12.2, necesitamos interactuar múltiples qubits. La compuerta más común es la **CNOT** (Controlled-NOT). Esta compuerta aplica una operación $X$ en un qubit *objetivo* (target) si y solo si el qubit *control* (control) está en el estado $|1\rangle$.

$$
\text{CNOT} = \begin{pmatrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 0 & 1 \\ 0 & 0 & 1 & 0 \end{pmatrix}
$$

El conjunto formado por $\{H, S, \text{CNOT}\}$ genera el **Grupo de Clifford**. Este grupo es fundamental para la corrección de errores (como los códigos CSS y qLDPC que estudiarás) porque las compuertas de Clifford mapean operadores de Pauli a otros operadores de Pauli. Según el Teorema de Gottesman-Knill, cualquier circuito compuesto exclusivamente por compuertas de Clifford y mediciones en la base computacional puede ser simulado eficientemente de manera clásica. Esta será la base matemática de tus simuladores de extracción de síndrome.

## 2.2 La Fragilidad de la Información Cuántica: El Desfase

Un bit clásico solo puede sufrir un tipo de error: el cambio de bit (bit-flip, $0 \leftrightarrow 1$). Un qubit, al ser un vector continuo, puede sufrir errores continuos. 

El entorno interacciona constantemente con los sistemas cuánticos. Una de las interacciones más destructivas causa el **desfase** (dephasing). Imagina un estado en el ecuador de la esfera de Bloch: $|\psi\rangle = \frac{1}{\sqrt{2}}(|0\rangle + e^{i\phi}|1\rangle)$. Si el qubit interactúa con un entorno no controlado (fluctuaciones magnéticas, térmicas), el ángulo $\phi$ sufre pequeñas variaciones aleatorias continuas a lo largo del tiempo.

Con el tiempo, promediado sobre todas las interacciones del entorno, la información de la fase relativa $\phi$ se pierde por completo. Matemáticamente, el vector de estado se "encoge" hacia el eje Z, perdiendo su pureza y convirtiéndose en un estado clásico probabilístico. El qubit "olvida" su superposición.

## 2.3 Modelado de Ruido Estocástico de Pauli

Modelar interacciones continuas es computacionalmente inviable para la arquitectura de sistemas a gran escala. Afortunadamente, la mecánica cuántica nos permite discretizar el error. Cualquier operación de ruido en un qubit se puede expandir en la base de las matrices de Pauli $\{I, X, Y, Z\}$.

En lugar de modelar pequeñas rotaciones continuas, los teóricos de la información cuántica utilizan modelos estocásticos. El más común es el **canal de despolarización** o canal de ruido de Pauli.

Asumimos que durante una operación (o tiempo de inactividad), ocurre un error discreto de manera probabilística. En el modelo de ruido de Pauli independiente:

*   Con probabilidad $(1 - p)$, no ocurre ningún error (se aplica la identidad $I$).
*   Con probabilidad $p_x$, ocurre un error de bit-flip ($X$).
*   Con probabilidad $p_y$, ocurre un error simultáneo de bit y fase ($Y = iXZ$).
*   Con probabilidad $p_z$, ocurre un error de fase ($Z$).

Donde la probabilidad total de error es $p = p_x + p_y + p_z$.

### Fallas de Ubicación Única y Propagación

Cuando construyamos circuitos reales (como las cuadrículas de hardware y topologías *heavy-hex*), inyectaremos este ruido probabilísticamente en ubicaciones específicas (compuertas, inicializaciones o mediciones). 

Es imperativo notar que un pequeño error de Pauli puede propagarse de manera desastrosa. Por ejemplo, un error $X$ que ocurra en el qubit de control *antes* de una compuerta CNOT, se propagará a través de la CNOT afectando tanto al qubit de control como al objetivo *después* de la compuerta. Esta cascada de errores (error propagation) puede transformar fallas simples (single-location faults) en errores correlacionados intratables, socavando los beneficios de cualquier estrategia de enrutamiento o código qLDPC.

---

### Actividad Interactiva: Simulación de Ruido Estocástico de Pauli

**Objetivo:** Desarrollar un modelo de canal ruidoso algorítmico e inyectar fallas de ubicación única para observar la degradación de un estado puro. Esta será la base para tu simulador de Clifford en los meses posteriores.

**Instrucciones para el Notebook (`13_01_pauli_noise_simulation.ipynb`):**

1. Define una función `apply_pauli_channel(state, p_x, p_y, p_z)` utilizando generadores de números aleatorios (ej. `np.random.choice`).
2. La función debe aplicar matricialmente $X$, $Y$, $Z$, o $I$ al vector de estado `state` basándose en las probabilidades proporcionadas.
3. **Simulación de Monte Carlo:** Inicializa un estado puro $|+\rangle$. Pásalo a través del canal de ruido 1,000 veces (simulando 1,000 qubits independientes sujetos al mismo entorno).
4. **Análisis:** Calcula la fidelidad (producto interno al cuadrado) entre el estado inicial puro y cada uno de los estados ruidosos resultantes. Promedia estas fidelidades. Observa cómo al incrementar $p_z$, el promedio de la fidelidad decrece, demostrando algorítmicamente la vulnerabilidad del estado a la pérdida de fase sin utilizar modelos continuos.