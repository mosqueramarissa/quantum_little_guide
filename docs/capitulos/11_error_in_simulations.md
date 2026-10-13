# Capítulo 22: Propagación de Errores en Simulaciones y Análisis Final

En nuestro recorrido, hemos partido de la abstracción matemática pura de los códigos estabilizadores hasta enfrentarnos a las severas restricciones geométricas de los procesadores cuánticos. En los Capítulos 20 y 21, descubrimos que mapear un código qLDPC complejo a un hardware físico exige la inserción de compuertas de enrutamiento SWAP. Sin embargo, la métrica definitiva del éxito de una arquitectura no es simplemente minimizar el recuento de SWAPs, sino garantizar la supervivencia de la información cuántica. 

Este capítulo final aborda el núcleo de tu investigación: cómo las decisiones de *embedding* y enrutamiento determinan microscópicamente la propagación de fallas en el circuito y, en última instancia, la probabilidad de falla lógica del sistema.

## 22.1 Modelos de Ruido a Nivel de Circuito

En las simulaciones teóricas simplificadas (ruido fenomenológico), los errores se aplican artificialmente a los qubits de datos de forma independiente. No obstante, para evaluar verdaderamente una arquitectura de hardware, debemos proponer un estudio de la propagación inducida por el circuito, en lugar de inferir la propagación basándonos únicamente en el grado del grafo.

En los modelos de ruido a nivel de circuito, las fallas (*faults*) no son eventos abstractos, sino anomalías físicas que pueden ocurrir en cualquier etapa de la ejecución:
1.  **Errores de compuerta:** Fallas estocásticas de Pauli que ocurren al aplicar operaciones unitarias. Los errores en compuertas de dos qubits (como CNOT o CZ) son particularmente críticos porque pueden crear errores correlacionados en ambos qubits participantes.
2.  **Errores de inactividad (*Idle errors*):** Mientras un qubit espera a que otro sea enrutado mediante SWAPs, su estado es vulnerable a la decoherencia (pérdida de fase y relajación de energía)[cite: 2]. La profundidad del circuito, aumentada por el enrutamiento, exacerba estos errores.
3.  **Errores de preparación y medición:** Fallas al inicializar los ancillas o al leer sus estados finales[cite: 2].

## 22.2 Mecanismos de Propagación y Errores tipo "Hook"

El fenómeno más destructivo en la corrección de errores cuánticos es la propagación de un error de ubicación única (*single-location fault*) en un error múltiple inmanejable. 

Consideremos una compuerta CNOT, donde el qubit de control es un ancilla y el qubit objetivo es un dato. Si ocurre un error $X$ en el ancilla justo antes de la compuerta, la acción de la CNOT propagará este error al qubit de datos. Un solo evento físico (un fotón térmico descarriado, por ejemplo) ha corrompido dos qubits lógicos simultáneamente.

La severidad de esta propagación de errores depende íntimamente de la estructura del circuito generado[cite: 2]. Específicamente, depende de:
*   **El ordenamiento y la dirección de las compuertas CNOT o CZ:** Un mal ordenamiento puede causar que un solo error en un ancilla se propague a múltiples qubits de datos, creando un patrón de error que el decodificador no puede asociar al síndrome extraído[cite: 2].
*   **Mecanismos de error tipo "Hook" (*Hook-error mechanisms*):** Son fallas que ocurren dentro del circuito de extracción de un estabilizador y que se propagan al bloque de código como un error de peso 2 o mayor, engañando a la distancia topológica protectora del código[cite: 2].
*   **La reutilización de ancillas (*Ancilla reuse*):** Reciclar qubits medidores sin la debida reinicialización o aislamiento puede arrastrar historiales de errores entre diferentes ciclos de síndrome[cite: 2].

## 22.3 La Ilusión de la Optimización de Distancia

Una conclusión fundamental que estructura tu investigación es que, para códigos qLDPC pequeños, el *embedding* que minimiza el conteo de compuertas SWAP no necesariamente minimiza la probabilidad de falla lógica[cite: 2]. 

¿Por qué ocurre esto? Un algoritmo voraz (*greedy*) podría lograr minimizar la distancia física agrupando todas las operaciones densamente, pero forzando un cronograma (*scheduling*) de ancillas ineficiente. Este cronograma podría aumentar localmente la profundidad del circuito o generar colisiones que favorezcan la propagación de fallas correlacionadas[cite: 2]. 

Por lo tanto, la optimización debe ser un equilibrio multivariable: debemos comparar arquitecturas locales, enrutadas, y aquellas con acopladores de largo alcance (*long-range couplers*) analizando la relación directa entre las métricas del grafo y la propagación real del error en simulaciones[cite: 2].

## 22.4 Estimación de la Probabilidad de Falla Lógica

El entregable culminante del flujo de trabajo es estimar la probabilidad de falla lógica para tamaños de código pequeños[cite: 2]. Esto se logra mediante el método de Monte Carlo:
1.  Inyectar ruido de Pauli estocástico en circuitos de síndrome simulados utilizando simuladores de Clifford.
2.  Extraer el historial del síndrome ruidoso.
3.  Pasar este historial a un algoritmo decodificador (como Minimum Weight Perfect Matching).
4.  Aplicar la corrección sugerida y verificar si el estado lógico resultante es el estado original o si hemos sufrido una falla lógica (una operación $\bar{X}$ o $\bar{Z}$ inadvertida).

Como señala tu rúbrica, la capacidad del decodificador para interpretar el síndrome resultante es un factor crucial en este paso[cite: 2]. Es importante mantener la rigurosidad científica recordando que la intención de este proyecto es reportar resultados de simulación de tamaño finito y *proxies* de errores lógicos, no establecer umbrales de hardware definitivos (*hardware thresholds*)[cite: 2]. 

---

## Práctica Interactiva: Simulación de Propagación de Fallas (Hook Error)

Para ilustrar de manera no pasiva cómo la dirección de una compuerta CNOT propaga un error y engaña al sistema (creando un error de peso 2 a partir de una falla de ubicación única), ejecutaremos una simulación estocástica en Qiskit.

```python
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel, pauli_error, depolarizing_error

# Demostración del mecanismo de propagación de un error X a través de un CNOT
def simular_propagacion_error():
    # 3 qubits: q0 (ancilla), q1 (dato A), q2 (dato B)
    qc = QuantumCircuit(3, 3)
    
    # 1. Inyección de una falla de ubicación única ANTES de las interacciones
    # Supongamos que un error X ocurre espontáneamente en el ancilla (q0)
    qc.x(0)
    qc.barrier()
    
    # 2. Circuito de extracción de paridad (el ancilla como control)
    qc.cx(0, 1)
    qc.cx(0, 2)
    
    # 3. Medición
    qc.measure([0, 1, 2], [0, 1, 2])
    
    # Ejecutar simulación
    simulator = AerSimulator()
    result = simulator.run(qc, shots=1).result()
    counts = result.get_counts(qc)
    
    print("=== Análisis de Propagación de Fallas ===")
    print("Se inyectó un error X únicamente en el Qubit Ancilla (q0).")
    print(f"Estado medido (q2 q1 q0): {list(counts.keys())[0]}")
    print("Observa cómo el qubit 1 y el qubit 2 ahora tienen el valor '1'.")
    print("¡El error físico único se ha propagado en un error lógico de peso 2!")

simular_propagacion_error()
```
### Referencias bibliográficas
Gottesman, D. (2010). "An introduction to quantum error correction and fault-tolerant quantum computation", en Quantum Information Science and Its Contributions to Mathematics.
Dennis, E., Kitaev, A., Landahl, A., & Preskill, J. (2002). "Topological quantum memory", Journal of Mathematical Physics, 43, 4452–4505.
Preskill, J. Quantum Computation, California Institute of Technology.