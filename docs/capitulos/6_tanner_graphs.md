# Capítulo 6: Grafos de Tanner y la Topología de la Incidencia

A medida que avanzamos hacia arquitecturas más complejas y eficientes, la representación matricial de los códigos estabilizadores ($H_X$ y $H_Z$) se vuelve opaca para el análisis geométrico y físico. Para diseñar procesadores tolerantes a fallas, necesitamos visualizar cómo interactúan exactamente los qubits lógicos con los medidores de síndrome. Esta necesidad nos lleva a la teoría de grafos, específicamente a los Grafos de Tanner, los cuales son la piedra angular matemática para entender la familia de códigos qLDPC (Quantum Low-Density Parity-Check).

## 6.1 Definición Matemática de un Grafo de Tanner

Un Grafo de Tanner es una representación visual y matemática de una matriz de comprobación de paridad clásica $H$ mediante un **grafo bipartito**. 

Un grafo bipartito $G = (V, E)$ es aquel donde el conjunto de vértices $V$ se puede dividir en dos subconjuntos disjuntos e independientes, $V_{data}$ y $V_{check}$, tal que cada arista en $E$ conecta un vértice en $V_{data}$ con uno en $V_{check}$. No existen aristas que conecten dos vértices del mismo subconjunto.

En el contexto de códigos de corrección de errores:
*   **Nodos de variable o datos ($V_{data}$):** Representan las columnas de la matriz $H$ (los bits o qubits de datos). Habrá $n$ de estos nodos.
*   **Nodos de comprobación o medidores ($V_{check}$):** Representan las filas de la matriz $H$ (las ecuaciones de paridad o los estabilizadores). Habrá $n-k$ de estos nodos.
*   **Aristas ($E$):** Existe una arista entre el nodo de comprobación $i$ y el nodo de variable $j$ si y solo si el elemento $H_{ij} = 1$.

Esta representación revela inmediatamente la **localidad** del código. Un código LDPC (Low-Density Parity-Check) se define precisamente como un código cuyo Grafo de Tanner tiene un grado máximo (número de aristas conectadas a un nodo) pequeño y acotado por una constante, independientemente del tamaño del sistema $n$.

## 6.2 Grafos de Tanner Cuánticos (para Códigos CSS)

Para un código cuántico CSS, la estructura bipartita se expande ligeramente porque tenemos dos matrices de paridad, $H_X$ y $H_Z$. 

El Grafo de Tanner Cuántico se compone de tres conjuntos de nodos:
1.  **Nodos de Qubits de Datos ($V$):** Representan los $n$ qubits físicos donde reside la información cuántica.
2.  **Nodos de Comprobación X ($C_X$):** Representan los generadores del estabilizador tipo X (filas de $H_X$). Estos detectan errores tipo Z.
3.  **Nodos de Comprobación Z ($C_Z$):** Representan los generadores del estabilizador tipo Z (filas de $H_Z$). Estos detectan errores tipo X.

El grafo sigue siendo bipartito si consideramos los conjuntos agregados: un lado contiene todos los qubits de datos ($V$) y el otro contiene todas las comprobaciones ($C_X \cup C_Z$). No existen aristas directas entre $C_X$ y $C_Z$, ni entre dos qubits de datos.

La condición fundamental de conmutatividad CSS ($H_X H_Z^T = 0 \pmod 2$) se traduce visualmente en el Grafo de Tanner: para cualquier nodo de comprobación $x \in C_X$ y cualquier nodo de comprobación $z \in C_Z$, el número de nodos de datos $v \in V$ que comparten (su vecindario común) debe ser par. 

## 6.3 Incidencia Lógica vs. Interacción Física

Es matemáticamente crítico, especialmente al diseñar arquitecturas de hardware tolerantes a fallas, distinguir entre el grafo del código y el procesador cuántico físico. 

**El Grafo de Tanner representa estrictamente la incidencia del código**; es decir, indica *qué* qubits de datos participan en *qué* comprobaciones de paridad[cite: 3]. Sin embargo, **no es necesariamente idéntico al grafo de interacción física del hardware ni al circuito de extracción de síndromes**[cite: 3].

1.  **Restricciones Físicas:** El hardware tiene un grafo de acoplamiento físico (por ejemplo, una cuadrícula o una topología *heavy-hex*) que dicta qué qubits pueden aplicar compuertas de dos qubits (CNOT o CZ) de forma nativa[cite: 3]. 
2.  **Circuitos Dinámicos:** Para evaluar un nodo de comprobación de grado $d$ en el Grafo de Tanner, el circuito cuántico requiere introducir un qubit ancilla y ejecutar una secuencia temporal de $d$ compuertas entrelazantes[cite: 3].
3.  **El Problema del Enrutamiento (Routing):** Si el Grafo de Tanner exige que un nodo de comprobación se conecte a un qubit de datos que está geográficamente distante en el hardware físico, la implementación requerirá compuertas SWAP sucesivas para acercar los estados, aumentando dramáticamente la profundidad del circuito y exacerbando la propagación de errores[cite: 3].

Comprender esta brecha entre la incidencia topológica (Grafo de Tanner) y la ejecución nativa (Grafo de Hardware) es el núcleo del problema de *embeddings* (colocación) heurísticos que se estudiará en los próximos capítulos.

---

## Práctica Interactiva: Construcción del Grafo de Tanner en Python

De acuerdo con los requerimientos de desarrollo de herramientas para la simulación de códigos CSS y qLDPC[cite: 3], el primer paso es construir un módulo de programación orientado a objetos que tome una matriz de paridad y genere su Grafo de Tanner. 

Utilizaremos la librería `networkx` de Python para mapear la matriz de comprobación de paridad de Steane $\llbracket 7, 1, 3 \rrbracket$ que definimos en el Capítulo 16, y visualizaremos la topología de su incidencia.

```python
import numpy as np
import networkx as nx
import matplotlib.pyplot as plt

# Matriz de paridad clásica (usada para H_X y H_Z en el código de Steane)
H = np.array([
    [1, 0, 0, 1, 0, 1, 1],
    [0, 1, 0, 1, 1, 0, 1],
    [0, 0, 1, 0, 1, 1, 1]
])

def construir_grafo_tanner_css(H_X, H_Z):
    """
    Construye un Grafo de Tanner bipartito para un código CSS cuántico.
    """
    G = nx.Graph()
    n_qubits = H_X.shape[1]
    
    # 1. Añadir nodos de datos (qubits)
    data_nodes = [f"Q{j}" for j in range(n_qubits)]
    G.add_nodes_from(data_nodes, bipartite=0, type='data')
    
    # 2. Añadir nodos de comprobación y aristas para H_X
    for i in range(H_X.shape[0]):
        check_node = f"X_chk{i}"
        G.add_node(check_node, bipartite=1, type='x_check')
        for j in range(n_qubits):
            if H_X[i, j] == 1:
                G.add_edge(check_node, f"Q{j}")
                
    # 3. Añadir nodos de comprobación y aristas para H_Z
    for i in range(H_Z.shape[0]):
        check_node = f"Z_chk{i}"
        G.add_node(check_node, bipartite=1, type='z_check')
        for j in range(n_qubits):
            if H_Z[i, j] == 1:
                G.add_edge(check_node, f"Q{j}")
                
    return G

# Generamos el grafo
grafo_tanner = construir_grafo_tanner_css(H, H)

# --- Visualización ---
plt.figure(figsize=(10, 6))

# Identificamos los conjuntos para colorear
data_nodes = [n for n, d in grafo_tanner.nodes(data=True) if d['type'] == 'data']
x_checks = [n for n, d in grafo_tanner.nodes(data=True) if d['type'] == 'x_check']
z_checks = [n for n, d in grafo_tanner.nodes(data=True) if d['type'] == 'z_check']

# Disposición bipartita
pos = {}
pos.update((node, (1, index)) for index, node in enumerate(data_nodes))
pos.update((node, (0, index * 2)) for index, node in enumerate(x_checks))
pos.update((node, (2, index * 2)) for index, node in enumerate(z_checks))

# Dibujar nodos
nx.draw_networkx_nodes(grafo_tanner, pos, nodelist=data_nodes, node_color='lightblue', label='Qubits de Datos')
nx.draw_networkx_nodes(grafo_tanner, pos, nodelist=x_checks, node_color='salmon', label='Comprobaciones X')
nx.draw_networkx_nodes(grafo_tanner, pos, nodelist=z_checks, node_color='lightgreen', label='Comprobaciones Z')

# Dibujar aristas y etiquetas
nx.draw_networkx_edges(grafo_tanner, pos, alpha=0.5)
nx.draw_networkx_labels(grafo_tanner, pos, font_size=9)

plt.title("Grafo de Tanner del Código Cuántico de Steane [[7, 1, 3]]")
plt.axis('off')
plt.legend(scatterpoints=1, loc='upper center', bbox_to_anchor=(0.5, -0.05), ncol=3)
plt.show()

# Verificación de densidad (LDPC check)
grados = [d for n, d in grafo_tanner.degree()]
print(f"Grado máximo en el grafo: {max(grados)}")
print("A medida que escalemos a qLDPC, este grado máximo debe permanecer pequeño y constante.")
```

### Referencias bibliográficas
Tillich, J.-P., & Zémor, G. (2014). "Quantum LDPC codes with positive rate and minimum distance proportional to the square root of the blocklength", IEEE Transactions on Information Theory, 60, 1193–1202.
Breuckmann, N. P., & Eberhardt, J. N. (2021). "Quantum low-density parity-check codes", PRX Quantum, 2, 040101.