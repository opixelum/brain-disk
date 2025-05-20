# Matrices

## Diagonalization

### Eigen Values & Eigen Vectors

- Only square matrices have eigen values and eigen vectors;
- All square matrices have eigen values and eigen vectors;
- A matrix of size *n* x *n* has exactly *n* eigen values;
- A diagonal matrix has non-zero values on the diagonal, but null values
  everywhere else;
- A matrix is diagonalizable when $A = PDP^{-1}$, where $P$ is the
  change-of-basis matrix of $A$, and $D$ is the diagonal matrix of $A$;
- If a matrix is diagonalizable, its diagonal form strictly contains all its
  eigen values;
- A change-of-basis matrix of another matrix contains all its eigen vectors as
  columns;
- The order of the eigen vectors in a change-of-basis matrix is the same as the
  order than their associated eigen values in the corresponding diagonal matrix.
- To find the eigen values of $A$, solve $det(A - \lambdaI) = 0$, where A is the
  matrix, and $I$ is the identity matrix.
  Each possible solution (values of $/lambda$) is an eigen value of $A$.
  The set of all eigen values is called the spectrum of $A$.

## References

- M. Bianchini
