# 0.10 Some useful identities

El producto tensorial ($\otimes$) es la operación matemática que nos permite unir varios sistemas cuánticos aislados. Al aplicar el producto tensorial, se multiplican las dimensiones de los qubits para formar un sistema compuesto más grande. Si tenemos un espacio $\mathcal{H}_A$ de dimensión $n$ y $\mathcal{H}_B$ de dimensión $m$, el espacio compuesto $\mathcal{H}_A \otimes \mathcal{H}_B$ tiene dimensión $nm$.

Algunas identidades fundamentales para manipular operadores y estados compuestos son:

1. **Distribución escalar:** $c(A \otimes B) = (cA) \otimes B = A \otimes (cB)$
2. **Producto mixto:** Para operadores $A, B, C, D$, se cumple que:
   $$(A \otimes B)(C \otimes D) = (AC) \otimes (BD)$$
3. **Aplicación sobre estados:** Cuando operadores actúan sobre estados compuestos:
   $$(A \otimes B)(\vert{}\psi\rangle \otimes \vert{}\phi\rangle) = (A\vert{}\psi\rangle) \otimes (B\vert{}\phi\rangle)$$
4. **Adjunto y Traza del producto tensorial:**
   $$(A \otimes B)^\dagger = A^\dagger \otimes B^\dagger$$
   $$	ext{Tr}(A \otimes B) = 	ext{Tr}(A)	ext{Tr}(B)$$

---
**Referencias**
* Kaye, P., Laflamme, R., & Mosca, M. (2007). *An Introduction to Quantum Computing*. Oxford University Press. (Capítulo 2: The Quantum Mechanics of Qubit Systems).
