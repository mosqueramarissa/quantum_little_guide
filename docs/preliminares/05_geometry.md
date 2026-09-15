# 0.5 Geometría

La geometría de los espacios cuánticos se deriva de las propiedades del producto interno. A diferencia del espacio euclidiano clásico, la geometría cuántica abarca dimensiones complejas.

Para un sistema de dos niveles (un solo qubit), la restricción de normalización ($|\alpha|^2 + |\beta|^2 = 1$) y el hecho de que la fase global de un estado cuántico es inobservable (no afecta las probabilidades de medición), permiten parametrizar cualquier estado puro de un qubit usando dos ángulos reales, $\theta$ y $\varphi$:

$$|\psi\rangle = \cos\left(\frac{\theta}{2}\right)|0\rangle + e^{i\varphi}\sin\left(\frac{\theta}{2}\right)|1\rangle$$

Esta parametrización da lugar a la **esfera de Bloch**, una representación geométrica tridimensional donde todo estado puro corresponde a un punto en la superficie de una esfera unitaria. En esta geometría, la ortogonalidad tiene una representación visual peculiar: dos vectores ortogonales en el espacio de Hilbert complejo (donde $\langle\phi|\psi\rangle = 0$) se representan como puntos antípodas en polos opuestos de la esfera de Bloch (por ejemplo, los estados $|0\rangle$ y $|1\rangle$).

El valor absoluto del producto interno al cuadrado, $|\langle\phi|\psi\rangle|^2$, puede interpretarse geométricamente como la medida de la superposición (overlap) o la "cercanía" entre dos estados.

---
**Referencias**
* Bloch, F. (1946). *Nuclear Induction*. Physical Review.
* Nielsen, M. A., & Chuang, I. L. (2010). *Quantum Computation and Quantum Information* (10th Anniversary ed.). Cambridge University Press. (Capítulo 1: Introduction and Overview).
