# 0.1 Complex numbers

La mecánica cuántica se formula inherentemente sobre el cuerpo de los números complejos, $\mathbb{C}$. Un número complejo $z \in \mathbb{C}$ se define por su parte real e imaginaria:

$$z = x + iy$$

Donde $x, y \in \mathbb{R}$ e $i = \sqrt{-1}$. Una herramienta fundamental en la computación cuántica es el conjugado complejo, denotado como $z^*$, el cual invierte el signo de la parte imaginaria:

$$z^* = x - iy$$

El módulo (o magnitud) al cuadrado de un número complejo nos permite calcular probabilidades en sistemas cuánticos, y se define como el producto del número por su conjugado complejo:

$$|z|^2 = z z^* = x^2 + y^2$$

**Representación Polar**
Es común y útil representar los números complejos en su forma polar, usando la fórmula de Euler ($e^{i\theta} = \cos\theta + i\sin\theta$):

$$z = r e^{i\theta}$$

Donde $r = |z|$ es la magnitud y $\theta$ es la fase. Las fases relativas entre estados cuánticos son responsables de fenómenos como la interferencia, que es la base de algoritmos cuánticos acelerados.

---
**Referencias**
* Rieffel, E. G., & Polak, W. H. (2011). *Quantum Computing: A Gentle Introduction*. MIT Press. (Capítulo 2: Quantum Mechanics Basics).
