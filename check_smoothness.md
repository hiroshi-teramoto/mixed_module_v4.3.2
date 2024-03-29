Let us explain how to confirm if a given semi-algebraic set $V \left( E \right) \setminus V \left( N \right)$ defined by the ideals $E$ and $N$ is a smooth manifold by using the constant rank theorem by taking semi-algebraic sets appearing in the classificaiton of divergent diagram as examples. In the classification, we got the following list of semi-algebraic sets: 

| # | Semi-Algebraic Set |
| - |------------------- |
| 1 | $c_1 = c_2 = c_3 = c_4 = 0$ |
| 2 | $c_1 c_4 - c_2 c_3 \neq 0$ |
| 3 | $c_1 c_4 - c_2 c_3 = 0$ and ($c_2 c_4 \neq 0$ or $c_1 c_4 \neq 0$ or $c_1 c_3 \neq 0$)  |
| 4 | $c_2 c_4 = c_1 c_4 = c_2 c_3 = c_1 c_3 = 0$ and ($c_1 \neq 0$ or $c_2 \neq 0$ or $c_3 \neq 0$ or $c_4 \neq 0$)  |

Let $E$ and $N$ be ideal defining a semi-algebraic set. For example, the fourth semi-algebraic set can be written as $V \left( E \right) \setminus V \left( N \right)$ where 

$$E = \langle c_2 c_4, c_1 c_4, c_2 c_3, c_1 c_3 \rangle$$

and 

$$N = \langle c_1, c_2, c_3, c_4 \rangle$$

where $\langle \cdots \rangle$ is the ideal generated by $\cdots$ in $\mathbb{R} \left[ c_1, c_2, c_3, c_4 \right]$. In the source code ([check_smoothness.exe](https://github.com/hiroshi-teramoto/mixed_module/blob/main/check_smoothness.exe)), you can check the semi-algebraic sets in the list are smooth manifolds by using the constant rank theorem. 

Let $\mathbb{R} \left[ c \right]$ be a polynomial ring with variables $c = \left( c_1, \ldots, c_m \right)$ with the coefficient field $\mathbb{R}$ and $E$, $N$ be ideals in $\mathbb{R} \left[ c \right]$. Let $h_1, \ldots, h_l$ be generators of $E$. Let $dE$ be the $l \times m$ jacobi matrix defined as:

$$ dE = 
\begin{pmatrix}
\frac{\partial h_1}{\partial c_1} & \cdots & \frac{\partial h_1}{\partial c_m} \\
\vdots & \ddots & \vdots \\
\frac{\partial h_l}{\partial c_1} & \cdots & \frac{\partial h_l}{\partial c_m}
\end{pmatrix}.
$$

In the following line in the source code, 
```Singular
list L = gauss_elimination_gb(E,N,dE);
```
Gaussian elimination is done in the parameter range $V \left( E \right) \setminus V \left( N \right)$.

> ```Singular
> gauss_elimination_gb(ideal E, ideal N, matrix A);
> ```
> | Parameter | Description |
> | --------- | ----------- |
> | `E`, `N` | ideals specifying the parameter range |
> | `A` | matrix possibly containing parameters to be Gaussian eliminated |
> #### Output
> - decomposition of the parameter range along with the row echelon form on each decomposition
> - The output format is as follows:
> ```Singular
> [i]: information of row echelon form on the $i$-th parameter range $V(E_i)\V(N_i)$.
>  [i][1]: generators of $E_i$
>  [i][2]: generators of $N_i$
>  [i][3]: row echelon form of the matrix `A`
>  [i][4]: list of indices of independent row vectors
> ```

Specifically, `size(L[i][4])` is the size of list of indices of independent row vectors that is the rank of the matrix `dE` in the parameter range $V \left( E_i \right) \setminus V \left( N_i \right)$.

The remainder of the code, 
```Singular
list Lrank;
for(i=1;i<=size(L);i++){
	int thereis = 0;
	int rk = size(L[i][4]);

	for(j=1;j<=size(Lrank);j++){
		if(rk == Lrank[j][1]){
			thereis = 1;
			Lrank[j][2] = insert(Lrank[j][2],list(L[i][1],L[i][2]));
			break;
		}
	}
	if(!thereis){
		Lrank = insert(Lrank,list(rk,list(list(L[i][1],L[i][2]))));
	}
	kill thereis, rk;
}

for(i=1;i<=size(Lrank);i++){
	Lrank[i][2] = addcons(Lrank[i][2]);
	for(j=1;j<=size(Lrank[i][2]);j++){
		Lrank[i][2][j][1] = radical_ideal(Lrank[i][2][j][1]);
		Lrank[i][2][j][2] = reduction_by_ideal(Lrank[i][2][j][2],Lrank[i][2][j][1]);
		Lrank[i][2][j][2] = radical_ideal(Lrank[i][2][j][2]);
	}
}
```
computes the rank of the matrix `dE` in each parameter range and combine and simplifies parameter ranges on which the matrix `dE` has the constant rank. The result is saved in the list `Lrank`. The format of `Lrank` is as follows:

> ```Singular
> [i]: the information of the rank in the $i$-th parameter range
>  [i][1]: rank (= `rk`) of the matrix `dE` in the $i$-th parameter range
>  [i][2]: list of locally closed sets on which the rank of `dE` is `rk`
>  [i][2][j]: the information of the $j$-th locally closed set $V(E_{ij})\V(N_{ij})$
>  [i][2][j][1]: generators of ideal $E_{ij}$
>  [i][2][j][2]: generators of ideal $N_{ij}$
> ```

For example, for the third semi-algebraic set in the list, i.e. $E = \langle c_1 c_4 - c_2 c_3 \rangle$ and $N = \langle c_2 c_4, c_1 c_4, c_1 c_3 \rangle$, the output should be like, 
```Singular
Rank of the defining equations of E on V(E)V(N)
[1]:
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
```
which means for the parameter range $V \left( E \right) \setminus V \left( N \right)$, the rank of the rank of `dE` is $1$. By using the constant rank theorem, we can conclude that $V \left( E \right) \setminus V \left( N \right)$ is a smooth manifold. You can try the same computation for the other semi-algebraic sets as well by using the source code.
