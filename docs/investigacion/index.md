# Investigación: Hardware-Aware Graph Embeddings and Error Propagation in Small Quantum LDPC Code

Esta investigación se enmarca en la modalidad de Estancia de Investigación. El proyecto está diseñado para ejecutarse con una dedicación de al menos 4 horas por semana durante 15 semanas.

## Objetivo General
Dado un código qLDPC CSS pequeño y un grafo de acoplamiento de hardware $H$, ¿qué estrategia de colocación de qubits y enrutamiento minimiza un costo combinado que involucra la cantidad de operaciones SWAP, la profundidad del circuito y el número de qubits de datos afectados por un solo fallo en el circuito?

## Objetivos Específicos
Para alcanzar el objetivo general, el desarrollo del proyecto se divide en las siguientes metas puntuales:

- Un paquete de Python que construya códigos CSS/qLDPC pequeños y sus grafos de Tanner.
- Un módulo de embedding y enrutamiento consciente del hardware.
- Un simulador de Pauli de Clifford para circuitos de extracción de síndromes.
- Comparaciones numéricas de al menos tres grafos de hardware.
- Un estudio de la sobrecarga de operaciones SWAP frente a la severidad de la propagación de errores.
- Un informe breve que contenga experimentos reproducibles y limitaciones.
