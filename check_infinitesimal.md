In [the tutorial](https://github.com/hiroshi-teramoto/mixed_module/blob/main/check_smoothness.md), the semi-algebraic sets in the list,

| # | Semi-Algebraic Set |
| - |------------------- |
| 1 | $c_1 = c_2 = c_3 = c_4 = 0$ |
| 2 | $c_1 c_4 - c_2 c_3 \neq 0$ |
| 3 | $c_1 c_4 - c_2 c_3 = 0$ and ($c_2 c_4 \neq 0$ or $c_1 c_4 \neq 0$ or $c_1 c_3 \neq 0$)  |
| 4 | $c_2 c_4 = c_1 c_4 = c_2 c_3 = c_1 c_3 = 0$ and ($c_1 \neq 0$ or $c_2 \neq 0$ or $c_3 \neq 0$ or $c_4 \neq 0$)  |

are smooth manifolds. In this tutorial, we provide a source code ([check_infinitesimal.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/check_infinitesimal.exe)) to test if the tangent spaces of the manifolds are contained in $T \mathcal{G} \left( f \right) + \mathcal{M}_2^2 \mathcal{E}_2^2$. This can be done by computing the mixed standard basis of $T \mathcal{G} \left( f \right) + \mathcal{M}_2^2 \mathcal{E}_2^2$ and reduce the tangent vectors with respect to the standard basis (For detail, see the paper, 4.1.1)[^1]. If this test is failed, the dimension of the orbit is strictly smaller than the dimension of the semi-algebraic set. This happens when the moduli appears.

Let $\mathbb{R} \left[ c \right]$ be a polynomial ring with variables $c = \left( c_1, \ldots, c_m \right)$ with the coefficient field $\mathbb{R}$ and $E$, $N$ be ideals in $\mathbb{R} \left[ c \right]$. Let $h_1, \ldots, h_l$ be generators of $E$. Let $dE$ be the $l \times m$ jacobi matrix defined as:

$$ dE = 
\begin{pmatrix}
\frac{\partial h_1}{\partial c_1} & \cdots & \frac{\partial h_1}{\partial c_m} \\
\vdots & \ddots & \vdots \\
\frac{\partial h_l}{\partial c_1} & \cdots & \frac{\partial h_l}{\partial c_m}
\end{pmatrix}.
$$

If the rank of the Jacobi matrix is constant over $V \left( E \right) \setminus V \left( N \right)$, the dimension of the kernel of the linear mapping $\mathbb{R}^m \rightarrow \mathbb{R}^l$ represented by the matrix $dE$ is constant and the kernel coinsides with the tangent space of the semi-algebraic set $V \left( E \right) \setminus V \left( N \right)$. First, we find a generator of the kernel `tvecs` and find a basis of the kernel by the command, 

```Singular
list L = gauss_elimination(E,N,transpose(tvecs));
```

Then, for each $i$, `L[i][3]` is the row echelon form of the transposed matrix `tvecs` over the parameter range `V(L[i][1]) \ V(L[i][2])`. The set of non-zero row vectors of `L[i][3]` is a basis of the tangent space of $V \left( E \right) \setminus V \left( N \right)$ over the parameter range `V(L[i][1]) \ V(L[i][2])`. Suppose $v = \left( v_1, \cdots, v_m \right)$ be a one of the non-zero row vectors, the corresponding vector in the tangent space of the $1$-jet space is 

$$ v_1 \frac{\partial j^1 f}{\partial c_1} + \cdots + v_m \frac{\partial j^1 f}{\partial c_m} $$

This conversion is done in the code as:

```Singular
module C = TW*transpose(L[i][3]);
```

The remainder of the code checks if each basis is contained in $T \mathcal{G} \left( f \right) + \mathcal{M}_2^2 \mathcal{E}_2^2$. This can be checked by reducing the membership problem to a membership problem of a mixed module, latter can be solved by computing the mixed standard basis of the mixed module. For detail, please see the code ([check_infinitesimal.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/check_infinitesimal.exe)).

[^1]: If this test is failed, the dimension of the orbit is strictly smaller than the dimension of the semi-algebraic set. This happens when the moduli appears. In the current version of this library, we have not yet implemented a systematic classification algorithm in this case.
