## Diagonalization

### Eigenvalues & Eigenvectors

- Only square matrices have eigenvalues and eigenvectors;
- All square matrices have eigenvalues and eigenvectors;
- A matrix of size $n \times n$ has exactly $n$ eigenvalues;
- A diagonal matrix has non-zero values on the diagonal, but null values everywhere else;

- A matrix is diagonalizable when: $$A = PDP^{-1}$$where:
	- $P$ is the change-of-basis matrix of $A$;
	- and $D$ is the diagonal matrix of $A$.

- If a matrix is diagonalizable, its diagonal form strictly contains all its eigenvalues;
- A change-of-basis matrix of another matrix contains all its eigenvectors as columns;
- The order of the eigenvectors in a change-of-basis matrix is the same as the order than their associated eigenvalues in the corresponding diagonal matrix.

- To find the eigenvalues of $A$, solve: $$det(A - \lambda I) = 0$$where:
	- A is the matrix;
	- $\lambda$ is the unknown, a.k.a. the eigenvalue;
	- and $I$ is the identity matrix.
	
  Each possible solution (values of $\lambda$) is an eigenvalue of $A$. The set of all eigenvalues is called the spectrum of $A$, denoted by $S_p(A)$.

- To find the eigenvectors of A, solve, for each eigenvalue:
  $$
   (A - \lambda I)X = 0
   $$
  where:
	- A is the matrix;
	- $\lambda$ is the corresponding eigenvalue;
	- and X is the unknown, a.k.a. the eigenvector.

  The set of solution of this equation (set of eigenvectors) is denoted $E_\lambda = Ker(A - \lambda I)$.
	
- The multiplicity of each eigenvalue is equal to the corresponding exponent in the characteristic polynomial. E.g.:
  $$
   \begin{gathered}
     P_A(\lambda) = (4 - \lambda)²(2 - \lambda) \\
     S_p(A) = \left\{2, 4\right\} \\
     m(2) = 1, m(4) = 2
   \end{gathered}
   $$

- If all the eigenvalues have a multiplicity of 1, the matrix is diagonalizable;
- Diagonalization is useful for:
	- Easier calculation of power of matrices: $A^n = PD^nP^{-1}$;
	- Principal Component Analysis (PCA)
	
## References

- M. Bianchini (ESGI)
