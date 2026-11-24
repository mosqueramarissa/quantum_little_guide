# 0.1 Complex numbers

La mecánica cuántica se rige sobre el cuerpo de los números complejos, $\mathbb{C}$. Un número complejo es la combinación de un número real con un imagainario. Mientras un número real es cualquier numéro que puedas pensar, como:

$$ 1, \quad 13.5, \quad -0.768, \quad \frac{3}{2}, \quad \sqrt{2}$$

Los números imaginarios son aquellos que cuándo se elevan al cuadrado dan un resultado *negativo*. La unidad de un número imaginario es $i$

$$i = \sqrt{-1}$$

Cuando elevamos $i$ al cudrado sucecede lo siguiente:

$$i^2 = -1$$

Volviendo a los números complejo, así se ven,

$$ 1 + i, \quad 43 + 2i, \quad 0.95 - 3.2i, \quad -2 + \pi i$$

Entonces, ya tenemos nuestra primera definición:


$$z = a + bi$$

Donde $a, b \in \mathbb{R}$. La letra $z$ es usada a menudo para representar a los números complejos. Nos referirimos a la parte real y a la imaginaria usando **Re** e **Im** de esta manera:
$$\begin{align*}
    Re(z) &= a
    Im(z) &= b
\begin{align*}$$

Una herramienta fundamental en la computación cuántica es el conjugado complejo, denotado como $z^*$, el cual invierte el signo de la parte imaginaria.

$$z^* = x - bi$$


El módulo (también denominada magnitud) al cuadrado de un número complejo nos permite calcular probabilidades en sistemas cuánticos, y se define como el producto del número por su conjugado complejo:

$$|z|^2 = z z^* = x^2 + y^2$$

También es común representar los números complejos en su forma polar, usando la fórmula de Euler ($e^{i\theta} = \cos\theta + i\sin\theta$):

$$z = r e^{i\theta}$$

Donde $r = |z|$ es la magnitud y $\theta$ es la fase. Las fases relativas entre estados cuánticos son responsables de fenómenos como la interferencia, la cual es base para los algoritmos cuánticos acelerados.

---
**Referencias**

* Pierce, Rod. (2026). "Complex Numbers". Math Is Fun. Retrieved 22 Sep 2026 from https://www.mathsisfun.com/numbers/complex-numbers.html
* Rieffel, E. G., & Polak, W. H. (2011). *Quantum Computing: A Gentle Introduction*. MIT Press. (Capítulo 2: Quantum Mechanics Basics).
