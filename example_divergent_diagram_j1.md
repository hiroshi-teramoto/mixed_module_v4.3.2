Let us explain how to use the library by taking example_divergent_diagram_j1.exe (See 4.1.1 Example: Mather's lemma in the paper) as an example. Specifically, this example involves several parameters and through this example you can learn how to handle that. For background, please refer to 4.1.1 Example: Mather's lemma.

The purpose here is to classify $1$-jet of the divergent diagram 
$$\left( f_1; f_2 \right) \colon \left( \mathbb{R}, 0 \right) \xleftarrow{f_1} \left( \mathbb{R}^2, 0 \right) \xrightarrow{f_2} \left( \mathbb{R}, 0 \right)$$
by using Mather's lemma. The general form of the $1$-jet can be written as:
$$j^1 f_1 \left( x \right) = c_1 x_1 + c_2 x_2 + \mathcal{M}_2^2 \mathcal{E}_2^2$$
$$j^1 f_2 \left( x \right) = c_3 x_1 + c_4 x_2 + \mathcal{M}_2^2 \mathcal{E}_2^2$$

Through that expression, we can see that the $1$-jet space is coordinated by $c_1, c_2, c_3, c_4 \in \mathbb{R}$.

The group acting on the divergent diagram $\mathcal{G}$ induces the Lie group $j^1 \mathcal{G}$ acting on the $1$-jet space and we want to decompose the $1$-jet space into distinct orbits of the induced action. Since the dimension of every orbit is constant under the setting, we first decompose the $1$-jet space into several components so that 
$$\dim_{\mathbb{R}} T_{j^1 f} \left( j^1 \mathcal{G} \cdot j^1 f \right)$$
is constant for each component. This demonstration is to explain how to do that by using the library. 

By the isomorphism as an $\mathbb{R}$-vector spaces in the paper (4.1), we obtain 

$$j^1 \mathcal{M}_2 \mathcal{E} \_2 \^2 / T \_{j^1 f} \left( j^1 \mathcal{G} \cdot j^1 f \right) \cong \langle x_1, x_2 \rangle \left( \mathbb{R} \left[ X_1 \right] \_{\langle X_1 \rangle} \right)^2 / M$$

(Note that the right hand side of (4.1) should have $\langle x_1, x_2 \rangle$.), where 
- $I = \lbrace 1, 2, 3, 4 \rbrace$,
- $X_1 = \lbrace x_1, x_2, y_1, y_2 \rbrace$,
- $X_2 = \lbrace y_1 \rbrace$,
- $X_3 = \lbrace y_2 \rbrace$,
- $X_4 = \emptyset$,
and
- $M = \sum_{j \in I} M_j$ (Expressions of $M_j \ \left( j \in I \right)$ are lengthy and please refer to the paper for details.)

By using the isomorphism, the computation of the codimension of the tangent space $T \_{j^1 f} \left( j^1 \mathcal{G} \cdot j^1 f \right)$ is reduced to that of the mixed-module $M$. The latter can be computed by using the comprehensive standard system for mixed-module. 

First, let us define a ring having parameters `c(1)`, `c(2)`, `c(3)`, and `c(4)` which correspond to $c_1, c_2, c_3$, and $c_4$, respectively, with variables $\lbrace x_1, x_2, y_1, y_2 \rbrace$.
```Singular
ring R = (0,c(1..4)), (x(1..nx),y(1..ny)), (M(imat),C);
```

Then, let us define a family of variables `X` as:
```Singular
list X = list();
for(i=1;i<=ny;i++){
	X[i] = ideal(y(i));
	X[i] = std(X[i]);
}
X[ny+1] = ideal(0);
```

In the subsequent lines, we (for detail, see the code.)
- compute $\eta$,
- define the ideals $E$ and $N$,
- define $f$,
- define `TR1K` (= $M_1$),
- define `Q` (list of $M_2, M_3, M_4$, where `Q[i]` = $M_{i+1}$)

After the above parameters are set, we are readly to compute the comprehensive standard system for mixed-module:
```Singular
list Lg = cssm(X,eta,E,N,TR1K,Q);
```
For the detail of `cssm`, please refer to [example_codimension_transverse_fold.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/example_codimension_transverse_fold.md) or [the code](https://github.com/hiroshi-teramoto/mixed_module/blob/main/cssm_multi_v2.lib).

`cssm` decomposes the parameter space $\mathbb{C}^4$ into several locally closed sets $V \left( E_i \right) \setminus V \left( N_i \right)$ and the set of the standard system for mixed-module that is valid on each locally closed set. The codimension of the quotient space on each locally closed set is constant and it can be computed by 
```Singular
size(kbase_mixed(X,Lg[i]))-ny;
```
in the code. Note that in this case, we want to compute the codimension of $M$ in $\langle x_1, x_2 \rangle \left( \mathbb{R} \left[ X_1 \right] \_{\langle X_1 \rangle} \right)^2$ and thus we subtracted `ny` which corresponds to the monomials $\left( 1, 0 \right)$ and $\left( 0, 1 \right)$.

Lastly, combining the locally closed sets on which the codimension is the same, we obtain the desired decomposition. The function
> ```Singular
> addcons(Lcod[i][2])
> ```
> | Parameter | Description |
> | --------- | ----------- |
> | `Lcod[i][2]` | list of locally closed sets |
> #### Output
> simplified expression of union of the locally closed sets (For detail, refer to [Computing the Canonical Representation of Constructible Sets](https://link.springer.com/article/10.1007/s11786-016-0248-2) by Josep M. Brunat and Antonio Montes.

If you run this code, you will obtain the following output, where the output format is 
```Singular
> The format of Lg is as follows:
> ```Singular
> [i]: $i$-th decomposition of the $1$-jet space on which the codimension is constant
>  [i][1]: codimension of the tangent space
>  [i][2]: representation of constructible set (union of V(E_{ij})\V(N_{ij}) for j)
>    [i][j][1]: $E_{ij}$
>    [i][j][2]: $N_{ij}$
> ```

```Singular
[1]:
   [1]:
      4
   [2]:
      [1]:
         [1]:
            _[1]=(c(4))
            _[2]=(c(3))
            _[3]=(c(2))
            _[4]=(c(1))
         [2]:
            _[1]=1
[2]:
   [1]:
      0
   [2]:
      [1]:
         [1]:
            _[1]=0
         [2]:
            _[1]=(-c(1)*c(4)+c(2)*c(3))
[3]:
   [1]:
      1
   [2]:
      [1]:
         [1]:
            _[1]=(-c(1)*c(4)+c(2)*c(3))
         [2]:
            _[1]=(c(2)*c(4))
            _[2]=(c(1)*c(4))
            _[3]=(c(1)*c(3))
[4]:
   [1]:
      2
   [2]:
      [1]:
         [1]:
            _[1]=(c(2)*c(4))
            _[2]=(c(1)*c(4))
            _[3]=(c(2)*c(3))
            _[4]=(c(1)*c(3))
         [2]:
            _[1]=(c(4))
            _[2]=(c(3))
            _[3]=(c(2))
            _[4]=(c(1))
```

From this result, we obtain 
| Constructible Set | Codimension of $T \_{j^1 f} \left( j^1 \mathcal{G} \cdot j^1 f \right)$ |
| ----------------- | ----------------------------------------------------------------------- |
| $c_1 = c_2 = c_3 = c_4 = 0$ | $4$ |
| $c_1 c_4 - c_2 c_3 \neq 0$ | $0$ |
| $c_1 c_4 - c_2 c_3 = 0$ and ($c_2 c_4 \neq 0$ or $c_1 c_4 \neq 0$ or $c_1 c_3 \neq 0$)  | $1$ |
| $c_2 c_4 = c_1 c_4 = c_2 c_3 = c_1 c_3 = 0$ and ($c_1 \neq 0$ or $c_2 \neq 0$ or $c_3 \neq 0$ or $c_4 \neq 0$)  | $2$ |

This provides the decomposition of $\mathbb{C}^4$. The decomposition in real can be obtained by projecting the constructible sets to $\mathbb{R}^4$, since all the algorithms in this implementation are based upon arithmetic operations in the ground field only and thus if the scalars in the input data, that is, $c_1, \ldots, c_4$, are contained in $\mathbb{R}$, then all the scalars in the output also lie in $\mathbb{R}$. 

To complete the classification of the orbits of $1$-jet, 
- Confirm if each semi-algebraic set obtained by projecting the constructible set is manifold.
  - [check_smoothness.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/check_smoothness.md)
- Confirm if the tangent space of each semi-algebraic set is contained in $T \mathcal{G} \left( f \right) + \mathcal{M}_2^2 \mathcal{E}_2^2$.
  - [check_infinitesimal.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/check_infinitesimal.md)

If you confirm above, each connected components of the semi-algebraic sets are contained in a single orbit by using Mather's lemma. To find connected components of the semi-algebraic sets, you can use [CylindricalDecomposition](https://reference.wolfram.com/language/ref/CylindricalDecomposition.html) with the option "Components", for example. [Here](https://github.com/hiroshi-teramoto/mixed_module/blob/main/connectedcomponents.nb) is a sample Mathematica code to do that. Note that this code uses a random perturbation to ensure that the polynomials appearing in the cylindrical algebraic decomposition is quasi-monic. For detail, see Chapter 11, Remark 11.46 in [the book](https://link.springer.com/book/10.1007/3-540-33099-2) due to Saugata Basu, Richard Pollack, and Marie-Françoise Roy. Note that the algorithm involves a random number so run the algorithm repeatedly if you get some error (This happens if random numbers are chosen so that some of the polynomials appear in the cylindrical algebraic decomposition are not quasi-monic.). [SemialgebraicComponentInstances](https://reference.wolfram.com/language/ref/SemialgebraicComponentInstances.html) identifies a list of points for each connected component.

The results of the computation can be summarised as:

| Semi-Algebraic Set | # Connected Components | Representative $\left( c_1, c_2, c_3, c_4 \right)$ |
| ------------------ | ---------------------- | -------------------------------------------------- |
| $c_1 = c_2 = c_3 = c_4 = 0$ | $0$ | $\left( 0, 0, 0, 0 \right)$ |
| $c_1 c_4 - c_2 c_3 \neq 0$ | $2$ | $\left( 1, 0, 0, -1 \right)$, $\left( 1, 0, 0, 1 \right)$ |
| $c_1 c_4 - c_2 c_3 = 0$ and ($c_2 c_4 \neq 0$ or $c_1 c_4 \neq 0$ or $c_1 c_3 \neq 0$)  | $2$ | $\left( 1, 0, -1, 0 \right)$, $\left( 1, 0, 1, 0 \right)$ |
| $c_2 c_4 = c_1 c_4 = c_2 c_3 = c_1 c_3 = 0$ and ($c_1 \neq 0$ or $c_2 \neq 0$ or $c_3 \neq 0$ or $c_4 \neq 0$)  | $2$ | $\left( 1, 0, 0, 0 \right)$, $\left( 0, 0, 1, 0 \right)$ |

Here, we show one representative for each connected component. In terms of $1$-jet, the result can be written as:

| Representative $\left( c_1, c_2, c_3, c_4 \right)$ | $1$-jets |
| -------------------------------------------------- | -------- |
| $\left( 0, 0, 0, 0 \right)$ | $\left( 0, 0 \right)$ |
| $\left( 1, 0, 0, -1 \right)$, $\left( 1, 0, 0, 1 \right)$ | $\left( x_1, -x_2 \right)$, $\left( x_1, x_2 \right)$ |
| $\left( 1, 0, -1, 0 \right)$, $\left( 1, 0, 1, 0 \right)$ | $\left( x_1, -x_1 \right)$, $\left( x_1, x_1 \right)$ |
| $\left( 1, 0, 0, 0 \right)$, $\left( 0, 0, 1, 0 \right)$ | $\left( x_1, 0 \right)$, $\left( 0, x_1 \right)$ |

Since the induced action of $\mathcal{G}$ to the $1$-jet space transforms $\left( x_1, -x_2 \right)$ to $\left( x_1, x_2 \right)$, $\left( x_1, -x_1 \right)$ to $\left( x_1, x_1 \right)$, and $\left( 0, x_1 \right)$ to $\left( x_1, 0 \right)$[^1], respectively, we can get the following list of representative of each orbit. This reproduces the results in Table~1 in the paper.

| Representative of $1$-jet | Codimension of the orbit in the $1$-jet space |
| ------------------------- | --------------------------------------------- |
| $\left( x_1, x_2 \right)$ | $0$ |
| $\left( x_1, x_1 \right)$ | $1$ |
| $\left( x_1, 0 \right)$  | $2$ |
| $\left( 0, 0 \right)$  | $4$ |

For each representative, you can investigate the higher jets by using complete transversal. For the second representative, $\left( x_1, x_1 \right)$, its $2$-jet is investigated in [example_complete_transversal.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/example_complete_transversal.md). 

[^1]: Note that the original Mather's lemma only tells you whether a connected component is contained in a single orbit or not and whether more than one connected components are contained in a single orbit should be checked manually in the current version of this library.







