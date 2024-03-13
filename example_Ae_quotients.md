Let us explain how to use the library to compute Ae-codimension by taking example_Ae_quotients.exe as an example.

In this example, the definition of the base ring is as follows: 

```Singular
nx = 2;
ny = 2;
ring R = 0, (x(1..nx),y(1..ny)), (M(imat),C);
```

The set of all the variables is {x(1..nx), y(1..ny)}. In this example, `x` indicates the source variable of dimension `nx` and `y` indicates the target variable of dimension `ny`. `imat` is a matrix of integer entries to specify the monomial ordering. In this example, `imat` is a $(nx+ny) \times (nx+ny)$ matrix. In this example, `imat` specifies the negative degree lexicographical order satisfying $x_1 \prec \cdots \prec x_{nx} \prec y_1 \prec \cdots \prec y_{ny}$. For the detail of the matrix ordering, please refer to [B.2.6. Matrix orderings](https://www.singular.uni-kl.de/Manual/4-0-3/sing_896.htm). The command `(M(imat), C)` in the end of the ring definition specifies the module ordering on the top of the monomial ordering. Specifically, `C` represents the term over position ordering satisfying $\left( 1, 0 \right) \prec \left( 0, 1 \right)$. For detail, please refer to [3.3.3 Term orderings](https://www.singular.uni-kl.de/Manual/4-0-3/sing_31.htm).

First, you need to specify the family of variables $\left( X_i \right){i \in J}$ characterizing the structure of a mixed-module you want to manipulate. In this implementation, $X_1$ is the set of all the variables in the base ring. In this example, that is {x(1..nx), y(1..ny)}. You need to specify $X_2$. In the current implementation, X[i-1] = $X_i$ and the list of variables should be of type ideal. In this example, we want to compute $\mathcal{A}_e$-codimension of a map-germ $f \colon \left( \mathbb{R}^{nx}, 0 \right) \rightarrow \mathbb{R}^{ny}$, which is the codimension of the mixed-module $tf \left( \theta_{nx} \right) + \omega f \left( \theta_{ny} \right)$, where the first component $tf \left( \theta_{nx} \right)$ is $\mathcal{E}_{nx}$-module and the second component is $\mathcal{E}_{ny}$-module through $f$. To express the algebraic structure of the second component, let `X[1]` be the set of the target variables as, 

```Singular
list X = list();
X[1] = ideal(y(1..ny);
```

In the subsequent lines, $\eta$ in the paper (See 2. Setting in the paper) is computed for the given X[1], X[2], and X[3]. For example, if $X[1] \cap X[2] = X[3]$ holds, then eta[1,2] = 3.

Ideals $E$ and $N$ specify the entire range of parameters $V(E) \setminus V(N)$ in which comprehensive standard system is computed, where $V \left( E \right)$ and $V \left( N \right)$ are the zero sets of $E$ and $N$, respectively. Since the ideals are supposed to specify parameter ranges, they should only contain parameters as variables. If you want to compute a comprehensive standard system for all the parameter range, set 

```Singular
ideal E = 0; // zero ideal
ideal N = 1; // ideal containing 1, that is, R
```

Then, $V(E)$ is the whole parameter range and $V(N) = \emptyset$ holds, and thus $V(E) \setminus V(N)$ is the whole parameter range. 

In the subsequent lines, generators of each component of the mixed module is specified as follows (expression is lenghthy so please refer to the paper for detail) :

$M_1$ = TR1K;
$M_2$ = Q[1];
$M_3$ = Q[2];
$M_4$ = Q[3];

Finally, you are ready to compute comprehensive standard system for $(M_i)_{i \in \{ 1,2,3,4 \}}$. $M_1$ is supposed to have finite $K$-codimension. You can compute that by the following command (implemented in cssm_multi_v2.lib).

> ```Singular
> list Lg = cssm(X,eta,E,N,TR1K,Q);
> ```
> | Parameter | Description |
> | --------- | ----------- |
> | `X` | family of variables (Note that X[i-1] = $X_i$ in the paper) |
> | `eta` ( $\eta$ ) | matrix of positive integer entries that satisfies $X[i] \cap X[j] = X[\eta[i,j]]$ for all $i, j$ |
> | `E`, `N` | ideals to specify the parameter range $V \left( E \right) \setminus V \left( N \right)$ in which comprehensive standard system is computed |
> | `TR1K` | $M_1$ in the paper |
> | `Q` | list of modules (`Q[i]` corresponds to $M_{i+1}$ in the paper for $i \ge 2$) |
> #### Outputs
> The format of Lg is as follows:
> ```Singular
> [i]: information of mixed standard basis in the parameter range $V(E_i)\V(N_i)$.
>  [i][1]: generators of $E_i$
>  [i][2]: generators of $N_i$
>  [i][3]: information of local cohomologies and standard basis of $M_1$ in the parameter range $V(E_i)\V(N_i)$.
>    [i][3][1]: $E_i$
>    [i][3][2]: $N_i$
>    [i][3][3]: standard basis of $M_1$ ($S^{(1)}$ in the paper)
>    [i][3][4]: local cohomology of $M_1$
>  [i][4][j]: $S^{(j+1)}$ in the paper
> ```

The comprehensive mixed-standard system `Lg` can be used in the following functions implemented in cssm_multi_v2.lib:
> ```Singular
> reduce_mixed_with_E(list X, vector p, module Nc, list Q, ideal E)
> ```
> | Parameter | Description |
> | --------- | ----------- |
> | `X` | family of variables (Note that X[i-1] = $X_i$ in the paper) |
> | `p` | input vector to be reduced |
> | `Nc` | local cohomology of $M_1$ |
> | `Q` | list of mixed standard basis $S^{(j+1)}$ for $j \ge 1$ |
> | `E` | ideal in the polynomial ring of parameters (`Nc` and `Q` is defined on the parameter range $V(E) \setminus V(N)$ |
> #### Output
> - the reduced normal form of `p`, that is, $\mathrm{NF} \_{\textnormal{tail}} \left( p \middle| \left( S^{\left( j \right)} \right)_{j \in J} \right)$ in the paper
> #### Example
> Suppose `p` is a vector you want to reduce by the comprehensive standard system of the $i$-th parameter range $V(E_i) \setminus V(N_i)$, that is, `Lg[i]`. You can compute that by the command `reduce_mixed_with_E(X,p,Lg[i][3][4],Lg[i][4],Lg[i][3][1])`.

> ```Singular
> kbase_mixed(list X, list Lgi)
> ```
> | Parameter | Description |
> | --------- | ----------- |
> | `X` | family of variables (Note that X[i-1] = $X_i$ in the paper) |
> | `Lgi` | list of mixed standard basis in the $i$-th parameter range (`Lg[i]`) |
> #### Outputs
> - set of monomials not $X_i$-involutive multiple of $S^{(i)}$ for all $i \in J$
> #### Example
> The command `kbase_mixed(X,Lg[i])` outputs the set of monomials not $X_i$-involutive multiple of $S^{(i)}$ for all $i \in J$. The set is the basis of the quotient regarded as a $K$-vector space.
