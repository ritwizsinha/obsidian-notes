[[Linear Algebra]]


##### Dot Product 
1. A function from Rn x Rn -> R.
2. Range >= 0
3. f(x, y) = f(y, x) is commutative
4. Follows the triange inequality


### Length
Length/Norm of a vector is dot product with itself in any R^n


$$
\sqrt{\vec{x} \cdot \vec{x}}
$$
Two vectors are orthogonal if their dot product is 0. This follows from pythagorean theorem in n dimensions
$$
\lvert \lvert \vec{x} - \vec{y} \rvert   \rvert ^ 2  = \lvert \lvert \vec{x} \rvert  \rvert ^2 + \lvert \lvert \vec{y} \rvert  \rvert ^2
$$

Zero vector is orthogonal to every other vector out there.

##### Orthogonal Matrices

Matrix that does a perpendicular transformation of vectors without changing the size.

$$
A * A^{\mathrm{T}} = I = A^T * A
$$
$$

A^T = A^{-1}
$$


##### Orthogonal Decomposition
The orthogonal complement of a subspace W is all the vectors which are orthogonal to that subspace.
If we have an orthogonal basis for a subspace then we only need to check with these basis vectors instead of all vectors in teh subspace. 
The orthogonal component can be used to describe a 2 dimensional subspace in a three dimensional vector space, where the normal is the basis vector of the orthogonal subspace of U.

##### Orthogonal Projections
When we have too much multi dimensional data we project that data on a lower dimension feature space. This is a core concept of dimensionality reduction.

The orthogonal projections of a vector
	$$
\vec{x} \in \mathbb{R}^n

$$
in a subspace U
$$
U \subset \mathbb{R}^n
$$

Is the shortest distance for the vector x in the subspace.


If we have a matrix like Ax = B with no solution. The idea is to find the vector in the
subspace spanned by the columns of A that is closest to b, i.e., we compute
the orthogonal projection of b onto the subspace spanned by the columns
of A. This problem arises often in practice, and the solution is called the
least-squares solution. While solving Ax, it semantically means a linear combination of the span of A, which is present in the subspace itself 
#### Reducing to an orthogonal Basis

Gram Schmidt method is an algorithm for a set of basis vectors to be converted to orthogonal basis vectors.


W