# Capítulo 10: Enrutamiento Mediante Compuertas SWAP y la Sobrecarga de Conectividad

Incluso con el mejor algoritmo de *embedding* heurístico (como los explorados en el Capítulo 20), los códigos qLDPC de alta tasa inevitablemente exigirán interacciones entre qubits que no son físicamente adyacentes en el hardware. Cuando el Grafo de Tanner impone una comprobación de paridad entre dos nodos lógicos que están separados por una distancia $d_{hw} > 1$ en el procesador, el compilador cuántico debe intervenir alterando el circuito original. Esta intervención se realiza mediante el enrutamiento basado en compuertas SWAP.

Este capítulo formaliza cómo generamos los circuitos físicos de extracción de síndromes, introducimos el enrutamiento necesario y medimos el impacto directo en la profundidad del circuito.

## 10.1 La Compuerta SWAP y su Costo Físico

En computación cuántica, no podemos simplemente "leer" y "copiar" información de un lugar a otro debido al Teorema de No Clonación. Para mover el estado de un qubit lógico hacia un qubit físico adyacente al medidor de síndrome (ancilla), debemos intercambiar sus estados físicos utilizando la compuerta SWAP.

Algebraicamente, la compuerta SWAP sobre dos qubits permuta los estados base $\vert{}01\rangle$ y $\vert{}10\rangle$, dejando $\vert{}00\rangle$ y $\vert{}11\rangle$ inalterados. Su matriz de transformación es:
$$\text{SWAP} = \begin{bmatrix} 1 & 0 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix}$$

En la inmensa mayoría de las arquitecturas de hardware (como los circuitos superconductores), la compuerta SWAP no es una operación nativa. Se debe descomponer en una secuencia de tres compuertas CNOT (o su equivalente en compuertas CZ y rotaciones locales):
$$\text{SWAP}(A, B) = \text{CNOT}(A, B) \text{CNOT}(B, A) \text{CNOT}(A, B)$$

Esta descomposición implica que cada "salto" en el hardware triplica la probabilidad de error intrínseco asociado a una compuerta de dos qubits, además de incrementar el tiempo en que los demás qubits permanecen ociosos (sujetos a decoherencia).

## 10.2 Generación de Circuitos de Extracción de Síndromes

La extracción de un síndrome no es un evento instantáneo, sino un circuito dinámico[cite: 3]. Para medir un estabilizador de peso $w$ (por ejemplo, $Z^{\otimes 4}$ en el código de superficie), se requiere:
1.  **Preparación del estado:** Inicializar un qubit ancilla (generalmente en $\vert{}0\rangle$ para síndromes $Z$, o en $\vert{}+\rangle$ para síndromes $X$).
2.  **Entrelazamiento programado:** Ejecutar una secuencia ordenada de $w$ compuertas CNOT o CZ entre los qubits de datos y el ancilla. El ordenamiento y dirección de estas compuertas CNOT o CZ son críticos, ya que dictan cómo se propagan los errores a través del circuito.
3.  **Medición:** Medir el ancilla en la base computacional para colapsar y extraer la paridad.
4.  **Reutilización del ancilla:** En la corrección continua de errores, los ancillas se re-inicializan y se reutilizan, lo que añade otra capa de complejidad temporal (*ancilla reuse*).

Cuando mapeamos este circuito ideal al grafo físico, cualquier operación CNOT entre un qubit de datos y el ancilla que no compartan una arista física directa debe ser precedida y sucedida por redes de enrutamiento SWAP.

## 10.3 Sobrecarga de Enrutamiento y Profundidad del Circuito

Definimos dos métricas cruciales para evaluar el rendimiento de una arquitectura y su respectivo *embedding*:

*   **Sobrecarga de enrutamiento (*Routing Overhead*):** Es la proporción de compuertas SWAP insertadas respecto al número de compuertas lógicas originales. Si un circuito ideal requiere 10 CNOTs, pero el compilador inserta 15 SWAPs (es decir, 45 CNOTs adicionales) para satisfacer la topología del hardware, la sobrecarga es masiva.
*   **Profundidad del circuito (*Circuit Depth*):** Es el número máximo de pasos de tiempo desde el inicio hasta el final de la ejecución, asumiendo que las compuertas en qubits disjuntos pueden ejecutarse en paralelo. La profundidad del circuito crece linealmente con la inserción de SWAPs, exponiendo al sistema a errores de inactividad (*idle errors*).

Para evaluar si los códigos qLDPC justifican su uso sobre el código de superficie regular, es imperativo comparar numéricamente arquitecturas locales (como la cuadrícula 2D), arquitecturas enrutadas (como *heavy-hex* o redes 2D ejecutando códigos no locales mediante SWAPs) y arquitecturas con acopladores de largo alcance (donde conexiones distantes son permitidas físicamente).

---

## Práctica Interactiva: Inserción de SWAPs y Medición de Profundidad

Este bloque de código cumple con el objetivo de generar circuitos de extracción de síndromes, añadir enrutamiento basado en SWAP y medir empíricamente la profundidad del circuito y la sobrecarga resultante[cite: 3].

Utilizaremos el compilador (Transpiler) de Qiskit, el cual resuelve internamente el problema del enrutamiento estocástico cuando se le proporciona un mapa de acoplamiento (*Coupling Map*) restringido.

```python
import numpy as np
from qiskit import QuantumCircuit, transpile
from qiskit.transpiler import CouplingMap

def analizar_sobrecarga_enrutamiento(num_qubits, coupling_edges, edges_to_entangle):
    """
    Construye un circuito ideal de entrelazamiento, lo compila para una topología
    restringida y mide la sobrecarga de enrutamiento y la profundidad del circuito.
    """
    # 1. Construir el circuito ideal (sin restricciones físicas)
    qc_ideal = QuantumCircuit(num_qubits)
    
    # Supongamos que un medidor de síndrome (qubit 0) necesita interactuar
    # con los qubits indicados en edges_to_entangle
    ancilla = 0
    for target in edges_to_entangle:
        qc_ideal.cx(ancilla, target)
        
    depth_ideal = qc_ideal.depth()
    cnots_ideal = dict(qc_ideal.count_ops()).get('cx', 0)
    
    print(f"--- Circuito Ideal ---")
    print(f"Profundidad: {depth_ideal}")
    print(f"Número de CNOTs nativas: {cnots_ideal}")
    
    # 2. Definir el mapa de acoplamiento del Hardware
    # Si dos qubits no están conectados aquí, el transpiler insertará SWAPs
    coupling_map = CouplingMap(coupling_edges)
    
    # 3. Compilar (Transpilar) el circuito al hardware físico
    # optimization_level=1 realiza un enrutamiento básico
    qc_routed = transpile(qc_ideal, coupling_map=coupling_map, optimization_level=1, seed_transpiler=42)
    
    depth_routed = qc_routed.depth()
    ops_routed = dict(qc_routed.count_ops())
    cnots_routed = ops_routed.get('cx', 0)
    
    # Un SWAP insertado por Qiskit a nivel físico a menudo se representa como 3 CNOTs
    swaps_estimados = (cnots_routed - cnots_ideal) // 3
    
    print(f"\n--- Circuito Enrutado (Hardware-Aware) ---")
    print(f"Profundidad: {depth_routed}")
    print(f"Número total de CNOTs: {cnots_routed}")
    print(f"SWAPs estimados insertados: {swaps_estimados}")
    print(f"Factor de crecimiento de profundidad: {depth_routed / depth_ideal:.2f}x")
    
    return qc_ideal, qc_routed

# --- Experimento Numérico ---
print("Simulación de un estabilizador de peso 4 en una topología lineal restrictiva.")

# Hardware: Una línea recta 0-1-2-3-4 (solo conexiones a vecinos más cercanos)
hardware_lineal = [[0, 1], [1, 0], [1, 2], [2, 1], [2, 3], [3, 2], [3, 4], [4, 3]]

# El ancilla (0) debe hacer CNOT con los qubits 1, 2, 3 y 4.
# Físicamente, el 0 solo está conectado al 1. Para llegar a 2, 3 y 4, requerirá SWAPs.
qubits_de_datos_a_medir = [1, 2, 3, 4]

qc_ideal, qc_routed = analizar_sobrecarga_enrutamiento(
    num_qubits=5, 
    coupling_edges=hardware_lineal, 
    edges_to_entangle=qubits_de_datos_a_medir
)
```

### Referencias bibliográficas
Breuckmann, N. P., & Eberhardt, J. N. (2021). "Quantum low-density parity-check codes", PRX Quantum, 2, 040101.
Dennis, E., Kitaev, A., Landahl, A., & Preskill, J. (2002). "Topological quantum memory", Journal of Mathematical Physics, 43, 4452–4505.
