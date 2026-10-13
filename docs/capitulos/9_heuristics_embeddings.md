# Capítulo 9: *Embeddings* Heurísticos: Estrategias de Colocación de Qubits

Hemos llegado al núcleo algorítmico del diseño de hardware tolerante a fallas. En el Capítulo 6 construimos el Grafo de Tanner, que define la conectividad lógica ideal de un código, y en el Capítulo 8 modelamos las severas restricciones geométricas de los procesadores físicos mediante grafos de hardware (cuadrículas, *heavy-hex*, arquitecturas de dos capas). 

El problema ahora es cómo asignar (o "mapear") los nodos lógicos del Grafo de Tanner a los nodos físicos del hardware. Este desafío computacional se conoce como el problema de *embedding* de grafos o asignación de qubits (*qubit allocation*). Dado que encontrar la asignación óptima es un problema NP-duro, la investigación moderna se basa en algoritmos heurísticos para encontrar soluciones eficientes.

## 9.1 Formulación Matemática del Problema de *Embedding*

Sean $G_c = (V_c, E_c)$ el Grafo de Tanner del código cuántico y $G_{hw} = (V_{hw}, E_{hw})$ el grafo del hardware físico. 
Buscamos una función inyectiva $\pi: V_c \to V_{hw}$ que asigne cada qubit lógico y medidor de síndrome a un qubit físico único.

El objetivo es minimizar una función de costo $C(\pi)$. Una métrica de costo común es la suma de las distancias más cortas en el hardware para todas las aristas requeridas por el código:
$$C(\pi) = \sum_{(u, v) \in E_c} d_{hw}(\pi(u), \pi(v))$$
donde $d_{hw}(x, y)$ es la distancia geodésica (número de saltos) entre los nodos físicos $x$ e $y$ en el grafo $G_{hw}$. 

Si $d_{hw}(\pi(u), \pi(v)) > 1$, significa que la comprobación de paridad requiere interacciones no locales en el hardware, lo que obligará a insertar compuertas de enrutamiento (SWAPs) que incrementarán la profundidad del circuito y la probabilidad de error.

## 9.2 Estrategias de Colocación (Heurísticas)

Para códigos qLDPC complejos, donde el grado de los nodos y el grosor del grafo desafían la planaridad, se deben comparar numéricamente varias heurísticas de colocación de qubits. Las cinco familias principales de algoritmos son:

### 9.2.1 Colocación en Anchura (*Breadth-First Placement*)
Esta es una heurística constructiva directa. Se elige un nodo central o de alto grado en $G_c$ y se asigna al centro de $G_{hw}$. Luego, se recorre el grafo de Tanner mediante una búsqueda en anchura (BFS). A medida que se descubren nuevos vecinos lógicos, se asignan a los qubits físicos disponibles más cercanos al nodo padre en el hardware. Es computacionalmente muy rápido, pero a menudo queda atrapado en óptimos locales miopes.

### 9.2.2 *Embedding* Espectral de Grafos (*Spectral Graph Embedding*)
Esta técnica algebraica utiliza los valores propios (eigenvalores) y vectores propios (eigenvectores) de la matriz Laplaciana del grafo. 
La matriz Laplaciana se define como $L = D - A$, donde $D$ es la matriz diagonal de grados y $A$ es la matriz de adyacencia de $G_c$.
Los eigenvectores correspondientes a los eigenvalores más pequeños no nulos (los vectores de Fiedler) proporcionan coordenadas continuas que agrupan fuertemente a los nodos lógicos interconectados. Estas coordenadas continuas se discretizan luego para asignarlas a la topología física, minimizando naturalmente la dispersión espacial.

### 9.2.3 Recocido Simulado (*Simulated Annealing*)
Inspirado en la termodinámica, este algoritmo estocástico parte de una asignación aleatoria $\pi$ y propone pequeños intercambios de qubits (por ejemplo, intercambiar $\pi(u)$ con $\pi(v)$)[cite: 3]. 
Si el intercambio reduce el costo total (distancia de enrutamiento), se acepta. Si lo aumenta, se acepta con una probabilidad $P = \exp(-\Delta C / T)$, donde $T$ es una "temperatura" que disminuye gradualmente. Esto permite que el algoritmo escape de óptimos locales al principio, logrando mapeos altamente optimizados a costa de un mayor tiempo de ejecución.

### 9.2.4 Colocación Codiciosa basada en Congestión de Aristas (*Greedy Placement based on Edge Congestion*)
En arquitecturas restringidas, el problema no es solo la distancia pura, sino los cuellos de botella. Si muchas comprobaciones lógicas intentan enrutarse a través del mismo acoplador físico simultáneamente, los circuitos se retrasarán. Esta heurística asigna nodos evaluando no solo la distancia, sino penalizando las rutas físicas que ya están sobreutilizadas, equilibrando la "congestión" a lo largo de todo el chip.

### 9.2.5 Partición de Grafos seguida de Refinamiento Local (*Graph Partitioning followed by Local Refinement*)
Particularmente útil para las topologías de hardware de dos capas (*two-layer*) discutidas en el capítulo anterior. El grafo lógico $G_c$ se divide primero en dos (o más) subgrafos densamente conectados utilizando algoritmos de corte mínimo. Una partición se asigna a la capa superior del hardware y la otra a la capa inferior. Posteriormente, se aplican algoritmos locales para afinar las posiciones de los nodos dentro de su respectiva capa.

## 20.3 Consideraciones Críticas de Rendimiento

Es fundamental advertir que, para códigos qLDPC pequeños, el *embedding* que simplemente minimiza el número de compuertas SWAP no necesariamente minimiza la probabilidad de falla lógica. La profundidad del circuito, la programación de los ancillas, las fallas correlacionadas y la severidad de la propagación de errores también contribuyen significativamente y deben ser evaluadas empíricamente.

---

## Práctica Interactiva: Implementación y Comparación de *Embeddings*

El objetivo fundamental de este módulo es desarrollar un paquete en Python consciente del hardware (*hardware-aware*). A continuación, implementaremos un marco básico de *Simulated Annealing* para intentar mapear un pequeño código lógico a una cuadrícula física, minimizando la distancia de las aristas.

```python
import networkx as nx
import numpy as np
import random
import matplotlib.pyplot as plt

def simulated_annealing_embedding(G_logical, G_hw, initial_temp=100.0, cooling_rate=0.99, max_iter=1000):
    """
    Asigna nodos del grafo lógico al grafo de hardware minimizando la distancia de las aristas
    mediante el algoritmo heurístico de Simulated Annealing.
    """
    nodes_logical = list(G_logical.nodes())
    nodes_hw = list(G_hw.nodes())
    
    # Asignación inicial aleatoria (Inyectiva)
    mapping = dict(zip(nodes_logical, random.sample(nodes_hw, len(nodes_logical))))
    
    # Precomputar distancias de todos los pares en el hardware para optimizar velocidad
    dist_hw = dict(nx.all_pairs_shortest_path_length(G_hw))
    
    def calculate_cost(current_mapping):
        cost = 0
        for u, v in G_logical.edges():
            u_hw, v_hw = current_mapping[u], current_mapping[v]
            cost += dist_hw[u_hw][v_hw]
        return cost

    current_cost = calculate_cost(mapping)
    best_mapping = mapping.copy()
    best_cost = current_cost
    
    temp = initial_temp
    
    for iteration in range(max_iter):
        # Proponer un cambio: intercambiar la posición de dos nodos
        # Elegimos un nodo lógico asignado y un nodo físico aleatorio (asignado o libre)
        n1 = random.choice(nodes_logical)
        hw1 = mapping[n1]
        hw2 = random.choice(nodes_hw)
        
        # Encontrar si hw2 ya tiene un nodo lógico asignado
        n2 = next((k for k, v in mapping.items() if v == hw2), None)
        
        # Aplicar el intercambio propuesto
        new_mapping = mapping.copy()
        new_mapping[n1] = hw2
        if n2 is not None:
            new_mapping[n2] = hw1
            
        new_cost = calculate_cost(new_mapping)
        delta = new_cost - current_cost
        
        # Criterio de aceptación de Metropolis
        if delta < 0 or random.random() < np.exp(-delta / temp):
            mapping = new_mapping
            current_cost = new_cost
            
            if current_cost < best_cost:
                best_cost = current_cost
                best_mapping = mapping.copy()
                
        temp *= cooling_rate
        
    return best_mapping, best_cost

# --- Prueba del Algoritmo ---
# 1. Crear un pequeño grafo lógico (ej. Grafo de Tanner simplificado de grado 3)
G_logic = nx.random_regular_graph(3, 8, seed=42)

# 2. Crear un grafo físico (Cuadrícula 4x4, que tiene 16 nodos físicos)
G_physical = nx.grid_2d_graph(4, 4)

# 3. Ejecutar Simulated Annealing
mapping_resultado, costo_final = simulated_annealing_embedding(G_logic, G_physical)

print(f"Costo final de la asignación (Suma de distancias en hardware): {costo_final}")

# 4. Visualización del Mapping
pos_hw = {n: (n[0], n[1]) for n in G_physical.nodes()}
plt.figure(figsize=(8, 8))

# Dibujar hardware de fondo
nx.draw_networkx_nodes(G_physical, pos_hw, node_color='lightgray', node_size=200)
nx.draw_networkx_edges(G_physical, pos_hw, edge_color='lightgray')

# Dibujar nodos lógicos mapeados
pos_mapped = {node: pos_hw[mapping_resultado[node]] for node in G_logic.nodes()}
nx.draw_networkx_nodes(G_logic, pos_mapped, node_color='skyblue', node_size=400, edgecolors='black')
nx.draw_networkx_labels(G_logic, pos_mapped)

# Dibujar las aristas lógicas sobre el hardware para visualizar 'saltos' largos
nx.draw_networkx_edges(G_logic, pos_mapped, edge_color='red', width=2.0, alpha=0.7)

plt.title("Embedding Heurístico: Qubits Lógicos (Rojo/Azul) sobre Hardware (Gris)")
plt.axis('off')
plt.show()
```
### Referencias bibliográficas
Breuckmann, N. P., & Eberhardt, J. N. (2021). "Quantum low-density parity-check codes", PRX Quantum, 2, 040101.

