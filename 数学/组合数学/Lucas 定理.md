# [Lucas 定理](https://oi-wiki.org/math/number-theory/lucas)

对于质数$p$
$$
\begin{pmatrix}
 n \\
 m 
   \end{pmatrix} 
   =
\begin{pmatrix}
\lfloor n/p \rfloor \\
\lfloor m/p \rfloor
   \end{pmatrix}
\cdot
\begin{pmatrix}
 n\mod p \\
m\mod p 
   \end{pmatrix}
\mod p
$$

```cpp

```