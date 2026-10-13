# Pregunta de Investigación y Alcances

La base analítica de este proyecto busca responder la siguiente pregunta central: Dado un código qLDPC CSS pequeño y un grafo de acoplamiento de hardware $H$, ¿qué estrategia de asignación de qubits y enrutamiento minimiza un costo combinado que involucra la cantidad de operaciones SWAP, la profundidad del circuito y el número de qubits de datos afectados por un solo fallo en el circuito?

## Propagación de Errores y Ruido
Proponemos estudiar la propagación de errores inducida por el circuito, en lugar de inferir la propagación únicamente a partir del grado del grafo. En los modelos de ruido a nivel de circuito, los fallos pueden ocurrir en compuertas, ubicaciones inactivas, preparación de estados y mediciones. Adicionalmente, los errores en compuertas de dos qubits pueden generar fallos correlacionados en ambos qubits participantes. 

De forma general, la propagación de errores está fuertemente condicionada por:

- El orden y la dirección de las compuertas $CNOT$ o $CZ$.
- La reutilización de ancillas y la profundidad del circuito.
- Los mecanismos de "hook-error" y el ruido correlacionado de dos qubits.
- Los errores inactivos (idle errors) y los errores de medición.
- La capacidad analítica del decodificador para interpretar el síndrome resultante.

## Alcances y Limitaciones

- El grafo de Tanner representa la incidencia del código (es decir, qué qubits participan en qué verificaciones), pero no es necesariamente idéntico al grafo de interacción física o al circuito real de extracción de síndromes.
- Para los códigos qLDPC pequeños, el embedding que logra minimizar la cantidad de operaciones SWAP no necesariamente minimiza la probabilidad de fallo lógico, ya que existen factores como la profundidad del circuito, la programación de ancillas, los fallos correlacionados y la propagación de errores que también contribuyen significativamente.
- La intención fundamental de este proyecto es reportar resultados basados en simulaciones de tamaño finito y métricas de aproximación de errores lógicos, y no establecer umbrales definitivos de hardware.
- Incluso las demostraciones más recientes de memorias qLDPC de baja sobrecarga fundamentan sus resultados en códigos específicos, circuitos de síndrome particulares, ciertas suposiciones de ruido y decodificadores concretos; por lo tanto, el desempeño reportado en la literatura no debe generalizarse automáticamente a todos los Códigos de Tanner Cuánticos.
