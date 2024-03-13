Let us explain how to use the library by taking example_complete_transversal.exe (See 4.1.2 Example (complete transversal in the paper, with the degree filtration, for the class 3 in Table.~1) as an example. In this demonstration, the complete transversal is computed for the class 3 in Table.~1 in the paper, that is, 

$$ j^1 f \left( x \right) = \left( x_1, x_1 \right) + \mathcal{M}_2^2 \mathcal{E}_2^2.$$

Let $T \subset \mathcal{M}_2^2 \mathcal{E}_2^2$ be $\mathbb{R}$ -vector space satisfying 

$$\mathcal{M}_2^2 \mathcal{E}_2^2 \subset T + T \mathcal{G}_1 \left( f \right) + \mathcal{M}_2^3 \mathcal{E}_2^2.$$

where $\mathcal{G}_1$ is the group acting on divergent diagrams with $1$ -jet identity and $f = \left( x_1, x_1 \right)$ . The problem is reduced to find a basis of the quotient as a $\mathbb{R}$-vector space,

$$\mathcal{M}_2^2 \mathcal{E}_2^2 / \left( T \mathcal{G}_1 \left( f \right) + \mathcal{M}_2^3 \mathcal{E}_2^2 \right).$$

This quotient is isomorphic to $\langle x_1, x_2 \rangle^2 \left( \mathbb{R} \left[ X_1 \right] \_{\langle X_1 \rangle} \right)^2 / M$ , where 
- $I = \lbrace 1, 2, 3, 4 \rbrace$,
- $X_1 = \lbrace x_1, x_2, y_1, y_2 \rbrace$,
- $X_2 = \lbrace y_1 \rbrace$,
- $X_3 = \lbrace y_2 \rbrace$,
- $X_4 = \emptyset$,
and
- $M = \sum_{j \in I} M_j$ (Expressions of $M_j \ \left( j \in I \right)$ are lengthy and please refer to the paper for details.)

In this example, the definition of the base ring is as follows: 

```Singular
nx = 2;
ny = 2;
ring R = 0, (x(1..nx),y(1..ny)), (M(imat),C);
```

The set of all the variables is {x(1..nx), y(1..ny)}, which corresponds to $\lbrace x_1, x_2, y_1, y_2 \rbrace$ in the paper. `imat` is a matrix of integer entries to specify the monomial ordering. In this example, `imat` is a $4 \times 4$ matrix,

$$
\begin{pmatrix}
-1 & -1 & -1 & -1 \\
0 & 0 & 0 & 1 \\
0 & 0 & 1 & 0 \\
0 & 1 & 0 & 0
\end{pmatrix}
$$

This matrix specifies the negative degree lexicographical order satisfying $x_1 \prec x_2 \prec y_1 \prec y_2$. For the detail of the matrix ordering, please refer to [B.2.6. Matrix orderings](https://www.singular.uni-kl.de/Manual/4-0-3/sing_896.htm). The command `(M(imat), C)` in the end of the ring definition specifies the module ordering on the top of the monomial ordering. Specifically, `C` represents the term over position ordering satisfying $\left( 1, 0 \right) \prec \left( 0, 1 \right)$. For detail, please refer to [3.3.3 Term orderings](https://www.singular.uni-kl.de/Manual/4-0-3/sing_31.htm).

First, you need to specify the family of variables $(X_i)_{i \in J}$ characterizing the structure of a mixed-module you want to manipulate. In this implementation, $X_1$ is the set of all the variables in the base ring. In this example, that is {x(1..nx), y(1..ny)}, where x(i) and y(j) correspond to $x_i$ and $y_i$ in the paper. You need to specify $X_2$, $X_3$, and $X_4$. In the current implementation, X[i-1] = $X_i$ and the list of variables should be of type ideal. For example, 

```Singular
list X = list();
X[1] = ideal(y[1]);
X[2] = ideal(y[2]);
X[3] = ideal(0); // this correspond $X_3 = \emptyset$
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
> The format of `Lg` is as follows:
> ```Singular
> [i]: information of mixed standard basis in the parameter range $V(E_i)\V(N_i)$.
>  [i][1]: generators of $E_i$
>  [i][2]: generators of $N_i$
>  [i][3]: information of local cohomologies and standard basis of $M_1$ in the parameter range $V(E_i)\V(N_i)$.
>    [i][3][1]: $E_i$
>    [i][3][2]: $N_i$
>    [i][3][3]: local cohomology of $M_1$
>    [i][3][4]: standard basis of $M_1$ ($S^{(1)}$ in the paper)
>  [i][4][j]: $S^{(j+1)}$ in the paper
> ```

By using `Lg`, the complete transversal can be computed as follows (the parameter `K` to specify the degree of complete transversal you are looking for):

```Singular
list list_CT = list();
for(i=1;i<=size(Lg);i++){
	module HK1 = mx^K*freemodule(ny);
	module REM = HK1;
	for(j=1;j<=size(HK1);j++){
		REM[j] = reduce_mixed_with_E(X,REM[j],Lg[i][3][3],Lg[i][4],Lg[i][3][1]);	
	}
	if(size(REM) != 0){
		matrix A = matrix_representation_normalize(REM,kbase_mixed(X,Lg[i]));
		list L = gauss_elimination_gb(Lg[i][1],Lg[i][2],transpose(A));

		module CT;
		for(j=1;j<=size(L);j++){
			CT = 0;
			for(k=1;k<=size(L[j][4]);k++){
				CT = CT + HK1[L[j][4][k]];
			}
			list_CT = insert(list_CT,list(list(L[j][1],L[j][2]),CT));
		}
	}
	else{
		module CT;
		list_CT = insert(list_CT,list(list(Lg[i][1],Lg[i][2]),CT));
	}
}
```

In this code, `HK1` is the list of monomials of degree `K` with variables `x(1),...,x(nx)`. In the next subsequent lines, the monimials are reduced by the mixed modules. The quotient $\langle x_1, x_2 \rangle^K \left( \mathbb{R} \left[ X_1 \right] \_{\langle X_1 \rangle} \right)^2 / M$ is isomorphic to $\mathbb{R}$ - vector space spanned by the monomials in $\mathbb{R} \left[ X_1 \right]$ that are not $X_j$-involutive multiples of $S^{\left( j \right)}$ for all $j \in I$. The result is saved in the list `REM`. If all the elements of `REM` are zero, the complete transversal can be chosen to be the zero vector space and we are done. Else, by using the gaussian elimination, basis in `REM` is found. The result is saved in the list `list_CT`. The format of this list is as follows:

```Singular
[i]: list of complete transversal in the parameter range $V(E_i)\V(N_i)$.
 [i][1]:
   [i][1][1]: generators of $E_i$
   [i][1][2]: generators of $N_i$
 [i][2]: complete transversal in the parameter range $V(E_i)\V(N_i)$
   [i][2][j]: $j$-th basis of complete transversal
>
```

In the current example, the output should be like:

```Singular
complete transversal :
[1]:
   [1]:
      [1]:
         _[1]=0
      [2]:
         _[1]=1
   [2]:
      _[1]=x(1)*x(2)*gen(1)
      _[2]=x(2)^2*gen(1)
```

In this example, $1$ - jet does not contain any parameter and thus the range of parameters are the whole and a basis of the complete transversal is spanned by $\left( x_1 x_2, 0 \right)$ and $\left( x_2, 0 \right)$. The complete transversal theorem says that the orbit of the $2$ - jet of a map-gern whose $1$ - jet is $\left( x_1, x_1 \right) + \mathcal{M}_2^2 \mathcal{E}_2^2$ is $\mathcal{G}_1^2$-equivalent to 

$$\left( x_1 + c_1 x_1 x_2 + c_2 x_2^2, x_1 \right) + \mathcal{M}_2^3 \mathcal{E}_2^2$$

For this expression, you can use Mather's lemma to find distinct $\mathcal{G}^2$-orbit and proceed the classification further.
