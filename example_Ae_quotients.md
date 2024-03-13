Let us explain how to use the library to compute the quotient of the $\mathcal{A}_e$-tangent space of a map-germ $f \colon \left( \mathbb{R}^{nx}, 0 \right) \rightarrow \left( \mathbb{R}^{ny}, 0 \right)$ by taking example_Ae_quotients.exe as an example.

In this example, the definition of the base ring is as follows: 

```Singular
nx = 2;
ny = 2;
ring R = 0, (x(1..nx),y(1..ny)), (M(imat),C);
```

The set of all the variables is {x(1..nx), y(1..ny)}. In this example, `x` indicates the source variable of dimension `nx` and `y` indicates the target variable of dimension `ny`. `imat` is a matrix of integer entries to specify the monomial ordering. In this example, `imat` is a $(nx+ny) \times (nx+ny)$ matrix. In this example, `imat` specifies the negative degree lexicographical order satisfying $x_1 \prec \cdots \prec x_{nx} \prec y_1 \prec \cdots \prec y_{ny}$. For the detail of the matrix ordering, please refer to [B.2.6. Matrix orderings](https://www.singular.uni-kl.de/Manual/4-0-3/sing_896.htm). The command `(M(imat), C)` in the end of the ring definition specifies the module ordering on the top of the monomial ordering. Specifically, `C` represents the term over position ordering satisfying $e_1 \prec \cdots \prec e_{ny}$, where $e_i$ is the $i$-th standard basis of $\mathbb{R}^{ny}$. For detail, please refer to [3.3.3 Term orderings](https://www.singular.uni-kl.de/Manual/4-0-3/sing_31.htm).

First, you need to specify the family of variables $`\left( X_i \right)_{i \in J}`$ characterizing the structure of a mixed-module you want to manipulate. In this implementation, $X_1$ is the set of all the variables in the base ring. In this example, that is {x(1..nx), y(1..ny)}. You need to specify $X_2$. In the current implementation, X[i-1] = $X_i$ and the list of variables should be of type ideal. In this example, you want to compute the quotient space to the $`\mathcal{A}_e`$-tangent space of a map-germ $f$. The tangent space is the mixed-module $tf \left( \theta_{nx} \right) + \omega f \left( \theta_{ny} \right)$, where the first component $tf \left( \theta_{nx} \right)$ is $`\mathcal{E}_{nx}`$-module and the second component is $`\mathcal{E}_{ny}`$-module through $f$. To express the algebraic structure of the second component, let `X[1]` be the set of the target variables as, 

```Singular
list X = list();
X[1] = ideal(y(1..ny);
```

In the subsequent lines, $\eta$ in the paper (See 2. Setting in the paper) is set. In this example, there is only one components and eta[1,1] = 1.

Ideals $E$ and $N$ specify the entire range of parameters $V(E) \setminus V(N)$ in which comprehensive standard system is computed, where $V \left( E \right)$ and $V \left( N \right)$ are the zero sets of $E$ and $N$, respectively. Since the ideals are supposed to specify parameter ranges, they should only contain parameters as variables. If you want to compute a comprehensive standard system for all the parameter range, set 

```Singular
ideal E = 0; // zero ideal
ideal N = 1; // ideal containing 1, that is, R
```

Then, $V(E)$ is the whole parameter range and $V(N) = \emptyset$ holds, and thus $V(E) \setminus V(N)$ is the whole parameter range. 

In the subsequent lines, generators of each component of the mixed module is specified as follows (expression is lenghthy so please refer to the paper for detail) :

$M_1$ = TR1K;
$M_2$ = Q[1];

Finally, you are ready to compute comprehensive standard system for $(M_i)_{i \in \{ 1,2 \}}$. $M_1$ is supposed to have finite $K$-codimension. You can compute that by the following command (implemented in cssm_multi_std.lib).

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

The comprehensive mixed-standard system `Lg` can be used in the following functions implemented in cssm_multi_std.lib:

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
