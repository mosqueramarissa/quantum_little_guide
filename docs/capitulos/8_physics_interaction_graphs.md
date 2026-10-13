# Capítulo 8: Grafos de Interacción Física y Arquitecturas de Hardware

En los capítulos anteriores, exploramos cómo los grafos de Tanner y el producto de hipergrafos nos permiten diseñar códigos qLDPC matemáticamente robustos. Sin embargo, existe una brecha fundamental entre las matemáticas de la teoría de la información y la física de la materia condensada. El grafo de Tanner representa la incidencia del código, es decir, indica cuáles qubits participan en cuáles comprobaciones de paridad. No obstante, este grafo lógico no es necesariamente idéntico al grafo de interacción física real ni al circuito de extracción de síndromes que se ejecuta en el procesador cuántico[cite: 3]. 

Este capítulo marca el inicio de la **Parte V: Del Papel al Hardware**, abordando las limitaciones topológicas de los procesadores actuales y cómo modelarlas.

## 8.1 El Grafo de Acoplamiento Físico

Un procesador cuántico físico se modela mediante un **grafo de acoplamiento** o grafo de hardware $G_{hw} = (V_{hw}, E_{hw})$:
*   **Vértices ($V_{hw}$):** Representan las posiciones espaciales de los qubits físicos en el chip.
*   **Aristas ($E_{hw}$):** Representan los acopladores físicos (como resonadores de microondas en qubits superconductores) que permiten la ejecución nativa de compuertas de dos qubits (CNOT o CZ).

Si dos qubits no comparten una arista en $G_{hw}$, no pueden interactuar directamente. Esta restricción de localidad geométrica obliga a los ingenieros cuánticos a enfrentar un compromiso inevitable entre obtener buenos parámetros de codificación (alta tasa, gran distancia) y mantener la localidad geométrica requerida por el hardware.

## 8.2 Arquitecturas Base: Cuadrículas y Heavy-Hex

Para visualizar las restricciones, debemos analizar las topologías de hardware más comunes que buscaremos implementar y comparar computacionalmente[cite: 3].

### 8.2.1 La Cuadrícula Bidimensional (Grid)
La arquitectura de cuadrícula (grid) dispone los qubits en una matriz plana donde cada qubit interior tiene un grado máximo de 4 (conectado a sus vecinos Norte, Sur, Este y Oeste). Esta topología es el sustrato ideal y nativo para el código de superficie estudiado en el Capítulo 16, ya que los estabilizadores de peso 4 coinciden perfectamente con las caras (plaquettes) y vértices de la cuadrícula. Sin embargo, carece de la conectividad de largo alcance necesaria para las familias qLDPC modernas.

### 8.2.2 Topología Heavy-Hex
Desarrollada por IBM, la topología *heavy-hex* (hexagonal pesada) reduce la conectividad para mitigar el ruido cruzado (*cross-talk*) y las colisiones de frecuencia térmica. En este grafo, los qubits se disponen en un patrón de hexágonos donde las aristas tienen qubits adicionales insertados. El grado máximo de conectividad se reduce a 3 (en los nodos de bifurcación) y 2 (en los bordes). Implementar códigos lógicos en esta arquitectura requiere un enrutamiento sumamente cuidadoso, ya que los qubits de datos y de ancilla (medición) están mucho más distanciados en términos de saltos de red.

## 8.3 El Desafío qLDPC y las Arquitecturas de Dos Capas

Como se estableció en la literatura de Breuckmann y Eberhardt, los códigos qLDPC de alta tasa exigen grafos de Tanner con interacciones que violan la planaridad estricta[cite: 2]. 

Trabajos recientes han demostrado que ciertos códigos qLDPC de alta tasa poseen una conectividad de grado seis y grafos de Tanner con un grosor de dos (*thickness two*), en lugar de requerir un grafo arbitrario completamente no local[cite: 3]. El grosor de un grafo es el número mínimo de subgrafos planares en los que se pueden particionar sus aristas. 

El hecho de que estos grafos qLDPC complejos puedan descomponerse matemáticamente en exactamente dos subgrafos planares de grado tres sugiere fuertemente una posible implementación de hardware de múltiples capas (multilayer)[cite: 3]. En una arquitectura de **dos capas (two-layer)**, tendríamos dos chips planares apilados uno sobre el otro, permitiendo interacciones verticales nativas (grado 3 en la capa A + grado 3 en la capa B = grado 6 total) sin requerir cables largos o cruces físicos en un solo plano 2D.

## 8.4 Disparidad entre Código y Hardware

Cuando intentamos mapear un código qLDPC a un hardware restringido, el circuito de extracción de síndrome sufre severas penalizaciones:
1.  Si el grafo de Tanner exige medir una paridad entre qubits que no son vecinos en $G_{hw}$, debemos insertar **compuertas SWAP** sucesivas para intercambiar el estado cuántico a través de la red física hasta que los qubits sean adyacentes.
2.  Cada compuerta SWAP requiere 3 compuertas CNOT nativas, lo que aumenta la profundidad del circuito temporal[cite: 3].
3.  Una mayor profundidad del circuito y la presencia de compuertas SWAP exacerban dramáticamente la propagación de errores estocásticos de Pauli[cite: 3].

El desarrollo de un módulo de colocación (*embedding*) y enrutamiento consciente del hardware es vital para mitigar estos costos[cite: 3].

---

## Práctica Interactiva: Implementación de Grafos de Hardware

De acuerdo con nuestros objetivos de desarrollo, debemos implementar computacionalmente grafos de hardware en cuadrícula, tipo *heavy-hex* y de dos capas[cite: 3]. El siguiente código en Python, utilizando `networkx`, establece los cimientos para modelar los sustratos físicos sobre los cuales compararemos nuestras heurísticas de *embedding*.

Copia este código y visualiza las diferencias drásticas en conectividad (grado nodal) entre las distintas arquitecturas propuestas.

```python
import networkx as nx
import matplotlib.pyplot as plt

def generar_grafo_cuadricula(filas, columnas):
    """Genera un hardware físico con conectividad de cuadrícula 2D (grado max 4)."""
    G = nx.grid_2d_graph(filas, columnas)
    G.name = "Grid 2D"
    return G

def generar_grafo_heavy_hex_simple():
    """Genera un modelo simplificado de una celda Heavy-Hex (grado max 3)."""
    G = nx.Graph()
    G.name = "Heavy-Hex"
    # Hexágono base
    hex_nodes = [0, 2, 4, 6, 8, 10]
    # Qubits 'pesados' en las aristas
    heavy_nodes = [1, 3, 5, 7, 9, 11]
    
    # Conectando para formar el anillo hexagonal pesado
    for i in range(11):
        G.add_edge(i, i+1)
    G.add_edge(11, 0)
    
    # Añadiendo bifurcaciones (simulando conexiones a otros hexágonos)
    G.add_edge(0, 12)
    G.add_edge(4, 13)
    G.add_edge(8, 14)
    return G

def generar_grafo_dos_capas(filas, columnas):
    """
    Genera un modelo de hardware Multilayer (Dos Capas),
    sugerido para descomponer grafos qLDPC de grosor dos y grado seis.
    """
    G_capa1 = nx.grid_2d_graph(filas, columnas)
    G_capa2 = nx.grid_2d_graph(filas, columnas)
    
    G = nx.Graph()
    G.name = "Dos Capas (Multilayer)"
    
    # Renombrar nodos para evitar colisiones y añadir al grafo principal
    for n in G_capa1.nodes():
        G.add_node((n[0], n[1], 0), layer=0)
    for u, v in G_capa1.edges():
        G.add_edge((u[0], u[1], 0), (v[0], v[1], 0))
        
    for n in G_capa2.nodes():
        G.add_node((n[0], n[1], 1), layer=1)
    for u, v in G_capa2.edges():
        G.add_edge((u[0], u[1], 1), (v[0], v[1], 1))
        
    # Añadir acoplamientos inter-capa (eje Z, acopladores verticales)
    for r in range(filas):
        for c in range(columnas):
            G.add_edge((r, c, 0), (r, c, 1))
            
    return G

# --- Generación y Evaluación ---
hardware_graphs = [
    generar_grafo_cuadricula(4, 4),
    generar_grafo_heavy_hex_simple(),
    generar_grafo_dos_capas(3, 3)
]

plt.figure(figsize=(15, 5))

for i, G in enumerate(hardware_graphs):
    plt.subplot(1, 3, i+1)
    
    # Extraer el grado de los nodos para análisis métrico
    grados = [G.degree(n) for n in G.nodes()]
    grado_maximo = max(grados)
    
    if G.name == "Dos Capas (Multilayer)":
        # Disposición 3D proyectada a 2D para visualización
        pos = {(x,y,z): (x + z*0.3, y + z*0.3) for x,y,z in G.nodes()}
    else:
        pos = nx.spring_layout(G, seed=42)
        
    nx.draw(G, pos, with_labels=False, node_size=150, 
            node_color='lightcoral', edge_color='gray')
    plt.title(f"{G.name}\nGrado Máx: {grado_maximo}")

plt.tight_layout()
plt.show()
```

### Referencias bibliográficas
Breuckmann, N. P., & Eberhardt, J. N. (2021). "Quantum low-density parity-check codes", PRX Quantum, 2, 040101.
Mosquera Orellana, G. M. (2026). Hardware-Aware Graph Embeddings and Error Propagation in Small Quantum LDPC Code, Tecnológico de Monterrey, Política de Excelencia Académica de las Estancias.
Dennis, E., Kitaev, A., Landahl, A., & Preskill, J. (2002). "Topological quantum memory", Journal of Mathematical Physics, 43, 4452–4505.