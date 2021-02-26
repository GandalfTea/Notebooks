

## Linear Algebra
### Chapter 2


### 2.1 Scalars, vectors, Matrices and Tensors

The study of linear algebra involves several types of mathematical objects:

___Scalars___ are comprised of just a single number (in contrast to other objects in LA). They are written in italics. When defined, it is specified what kind of numbers they are : R - real number, N - natural number.
```
R - Real Numbers are numbers that can represent a distance along a line. 
They contain all rational numbers, such as integers (-5), fraction (4/3)
and irrational numbers (sqrt(2)). 

N - Natural Numbers are numbers that are positive, often used for counting. 
Controversy surrounds the ideea if it includes 0 or not.

All N numbers are in R.
```
   
   

___Vectors___ are comprised from an array of numbers. The numbers are arranged in order. We identify each number by it's index in that order. The first element of _x_ is _x_<sub>1</sub>, second is _x_<sub>2</sub>, etc. We must also specify what kind of numbers they are. If each element is in _R_, and the vector has _n_ elements, then the vector lies in the set formed by taking the Cartesian product of _R_ _n_ times, denoted as _R_<sup>n</sup>. 
```
Cartesian Product - It is a product between two sets of numbers (arrays).
It is calculated as the set of all possible ordered pairs with the first
element from X (first set) and the second from Y (second set).

X x Y = {(x,y): x is in X and y is in Y}

   Y  1     2     3   
  X 
-------------------------- X x Y
  x (x,1) (x,2) (x,3)

  y (y,1) (y,2) (y,3)

  z (z,1) (z,2) (z,3)

In this case, vector X is in R^3 because it has 3 elements.
```
When we need to explicitly identify the elements, we write them as a column like so:

TODO: Photo

We can think of vectors as identifying points in space, with each element giving the cooridinate along a different axis. 

Sometimes we need to index a set of elements of a vector. In this case, we define a set containing the indices and write the set as a subscript. For example, to access x<sub>1</sub>, x<sub>3</sub> and x<sub>6</sub>, we define the set _S_ = {1,3,6} and write ___x___<sub>S</sub>. We use the - sign to index the complement of a set. For example, ___x___<sub>-1</sub> is the vector containing all elements of ___x___ except for _x_<sub>1</sub>. Likewise, ___x___<sub>-S</sub> is a vector containing all elements of ___x___ except for the elements in _S_.

```
For an example:

X = [1, 3, 4, 6, 7, 8]
S = {1, 2, 6}
X.s = [1, 3, 8]
X.-s = [4, 6, 7]
````
     
     

___Matrices___ are a 2D array of numbers. Each element has two indices instead of one. They are usually named using uppercase bold typeface, such as ___A___. If a real-valued matrix (consisting only of real numbers) ___A___ has a height of _m_ and a width of _n_, we say that ___A___ is in _R_<sup>_m_ x _n_</sup>.        

We idenify elements with subscript, like this :
_A_<sub>1,1</sub>, _A_<sub>m,n</sub>.     
    
We identify all the elements in the row _i_ as _A_<sub>i, : </sub>     
Likewise, we identify all elements in the _i_ column as _A_<sub> : ,i</sub>    

When explicitly identifying the elements of a matrix, we display them like this:

TODO: Photo

Sometimes, we need to index matrix-valued expressions that are not just a single letter. We use subscript after the expression but do not convert anything to lowercase. For example _f_(___A___)<sub>i,j</sub> gives element (_i,j_) of the martix computed by applying a function _f_ to ___A___.

   
   
___Tensors___ are arrays of numbers arranged on a regular grid with a variable number of axes. We identify the element of ___A___ at coordinates (_i,j,k_)  by ___A___<sub>_i,j,k_</sub>.

```
All the above data types are of type tensor. That does not mean they should be reffered as such. 
A Scalar is a 0 dimensional tensor, array 1 dimmensional tensor and a matrix a 2 dimensional tensor. 
Strictly speaking we refer to a tensor as such if it has >= 3 dimensions.
```





An important operation on matrices is the __transpose__. The transpose of a matrix is the mirror image of the matrix across a diagonal line, called the __main diagonal__, running down and to the right, starting from the upper left corner. We denote the transpose of a matrix ___A___ as ___A___<sup>T</sup>, as in defined as: (___A___<sup>T</sup>)<sub>_i,j_</sub> = _A_<sub>_i,j_</sub>. A graphical representation of this process is:


TODO: Photo of matric diagonal line.

```
The transpose of a martix results in a new matrix where the rows of the original become 
the columns and the columns become the rows. As an example:

    A = [ 5  4    A^T = [ 5  4  7 -1
	  4  0		  4  0 10  8 ]
	  7 10
	 -1  8]

```
Vectors are matrices with a single column, therefore the transpose of a vector is a matrix with only one row. Sometimes we define a vector by writing it in the text inline as a matrix row, then use the transpose operator to turn it into a standard column vector. For example: ___x___ = [_x_<sub>1</sub>,_x_<sub>2</sub>, _x_<sub>3</sub>]<sup>T</sup>.

```
Vectors are just a 1 column or row matrix. A vector can exist in both directions.
When we refer to a matrix with one row or column, or a vector, we refer too the same thing.
There is no difference between them.


This means that weather we refer to the following as a one column or row matrix or vector, it is irrelevant.

A = [1, 2, 3, 4, 5 ... n]

A^T = [ 1
        2
	3
	...
	n ]
```


A scalar is esentially a matrix with a single entry, meaning the transpose of a scalar is itself: _a_ = _a_<sup>T</sup>.



We can add matrices to each other as long as they have the same shape by adding the coresponding elements : ___C___ = ___A___ + ___B___ where _C_<sub>_i,j_</sub> = _A_<sub>_i,j_</sub> + _B_<sub>_i,j_</sub>.


We can also add a scalar to a martix, or multiply a matrix by a scalar. This is done by performing the operation on each element of the matrix: ___D___ = _a_ * ___B___ + _c_ where _D_<sub>_i,j_</sub> = _a_ * _B_<sub>_i,j_</sub> + _c_.


In the context of deep learning we will also use some less conventional notation. We allow the addition of a matrix and a vector, resulting in another matrix: ___C___ = ___A___ + ___b___ where ___C___<sub>_i,j_</sub> = _A_<sub>_i,j_</sub> + _b_<sub>_j_</sub>. This is done by adding the vector to each row of the matrix. This eliminated the need to define a matrix wit ___b___ copied into each row during addition. The name of this operation, the implicit copying of ___b___ to many locations is called __broadcasting__. 




### 2.2 Multiplying Matrices and Vectors


Multiplication of matrices is one of the most important operation. When calculating the _matrix product_ of matrices ___A___ and ___B___, it results in a third matrix ___C___. In order for this to happen, ___A___ must have the same number of _columns_ as ___B___ has _rows_. For example, if ___A___ is of shape _m x n_ and ___B___ is of shape _n x p_, the resulting matrix ___C___ will be of shape _m x p_. We show this product like this: ___C___ = ___AB___. The product operation is defined by:

TODO: Matrix multiplication formula.

```
The dot product multiplication of two martices is done like so:

[ 1  2  3      [ 7   8 	    [ 58  64
  4  5  6]  X 	 9  10   =   139 154]
		 11 12]

The calculation for the first number is done by the first row of the first martix and the 
first column of the second martix. It is done like this:

(1, 2, 3) * (7, 9, 11) = 1 * 7 + 2 * 9 + 3 * 11 = 58

For the first row and second column:

(1, 2, 3) * (8, 10, 12) = 1 * 8 + 2 * 10 + 3 * 12 = 64
 
And so on for the remaining second row with both columns of the second matrix.
```

The standard product of two matrices is not just a matrix containing the product of the individual elements. Such a product is called __element-wise product__ or __Hadamard product__. 


The dot product between two vectors ___x___ and ___y___ of the same dimensionality is the matrix product ___x___<sup>T</sup>___y___. We can think of the matrix product ___C___ = ___AB___ as computing _C_<sub>_i,j_</sub> as the dot product between row _i_ of ___A___ an column _j_ of ___B___. (The rows and columns are esentially vectors)

```
To compute the dot product for two vectors of the same size, we turn it into a matrix dot product.
In order to do this, we transpose the first vector.
```


There are many useful properties that make mathematical analysis of matrices more conveniant. For example, matrix multiplication is distributive :    
___A___(___B___+___C___) = ___AB___ + ___AC___. 

  
It is also associative:
___A___(___BC___) = (___AB___)___C___.

```
Associative property : Rearanging the paranthesis will not change the result.
```

Matrix multiplication is not commutative (the condition ___AB___ = ___BA___ does not always hold), unlike scalar muliplication. However, the dot product of two vectors is commutative:
___x___<sup>T</sup>___y___ = ___y___<sup>T</sup>___x___.


The transpose of a matrix product has a simple form:

(___AB___)<sup>T</sup> = ___B___<sup>T</sup>___A___<sup>T</sup>



This allows us to demonstrate the equasion : ___x___<sup>T</sup>___y___ = ___y___<sup>T</sup>___x___ by exploiting the fact that the value of such a product is a scaler and therefore equal to its own transpose:


___x___<sup>T</sup>___y___ = (___x___ <sup>T</sup>___y___)<sup>T</sup> =  ___y___<sup>T</sup>___x___.



This is enough linear algebra to write down a system of linear equasions:



___Ax___ = ___b___

where ___A___ is a known vector and is in _R_<sup>_m_ x _n_</sup>, ___b___ is a known vector and in _R_<sup>_m_</sup> and ___x___ is a vector of unknown variables we would like to solve for in _R_<sup>_n_</sup>. Each element _x_<sub>_i_</sub> of ___x___ is one of those unknown variables. Each row of ___A___ and each element of ___b___ provide another constraint. We can rewrite the equasion from above as:      
___A___<sub>1 , : </sub>___x___ = _b_<sub>1</sub>      
___A___<sub>2 , : </sub>___x___ = _b_<sub>2</sub>       
. . .      
___A___<sub>_m_ , : </sub>___x___ = _b_<sub>_m_</sub>       


Or even more explicitly as :      
___A___<sub>1,1</sub>_x_<sub>1</sub> + ___A___<sub>1,2</sub>_x_<sub>2</sub> + . . . + ___A___<sub>1,_n_</sub>_x_<sub>_n_</sub> = _b_<sub>1</sub>

___A___<sub>2,1</sub>_x_<sub>1</sub> + ___A___<sub>2,2</sub>_x_<sub>2</sub> + . . . + ___A___<sub>2,_n_</sub>_x_<sub>_n_</sub> = _b_<sub>2</sub>

. . .


___A___<sub>_m_,1</sub>_x_<sub>1</sub> + ___A___<sub>_m_,2</sub>_x_<sub>2</sub> + . . . + ___A___<sub>_m_,_n_</sub>_x_<sub>_n_</sub> = _b_<sub>_m_</sub>



Matrix vector product notation provides a more compact representation for equasions of this form. 


### 2.3 Identity and Inverse Matrices

In linear algebra, there is a powerful tool called __matrix inversion__ that enables us to solve the equasion : ___Ax___ = ___b___  for many values of ___A___.



In order to understand matrix inversion, we first need to introduce the concept of an __identity matrix__. An identity matrix is a matrix that does not change any vector when we multply that vector by the matrix. We show the identity matrix that preserves _n_-dimensional vectors as ___I___<sub>_n_</sub>. Formally, ___I___<sub>_n_</sub> is in _R_<sup>_n_ x _n_</sup>, and for all ___x___ in _R_<sup>_n_</sup>,  ___I___<sub>_n_</sub>___x___ = ___x___.

```
The Identity Matrix is the equivalent of a 1 in simple calculations. 
It does not change the vector multiplied by it.

It can also be of any size. When multiplying with a vector, it must have the same size as the vector. 
It must also always be square.
```

The structure of the identity matrix is simple: all entries along the main diagonal are 1, while all the other entries are 0. 

```
[ 1 0 0      
  0 1 0      
  0 0 1 ]   
```



The matrix inverse of ___A___ is denoted as ___A___<sup>-1</sup> and is defined as the matrix such as that ___A___<sup>-1</sup>___A___ = ___I___<sub>_n_</sub>.

```
The matrix A and the inverse A^-1 must cancel each other out into an identity matrix.
```

We can now solve the equasion ___Ax___ = ___b___ by the following steps:      
    
___Ax___ = ___b___     
___A___<sup>-1</sup>___Ax___ =  ___A___<sup>-1</sup>___b___    (Multiply the inverse on both sizes) 
___I___<sub>_n_</sub>___x___ =  ___A___<sup>-1</sup>___b___    (They cancel out into an identity matrix) 
___x___ =  ___A___<sup>-1</sup>___b___.	   		       (Vector * identity matrix is the original vector)
    
  
This process is dependent on it being possible to find ___A___<sup>-1</sup>. We discuss the conditions for the existance of ___A___<sup>-1</sup> in the following section. 

When ___A___<sup>-1</sup> exists, many algorithms can find it in closed form. In theory, the same inverse matrix can be used to solve the equation for many different values of ___b___. It is primarely useful as a theoretical tool, and should not actually be used in practice for most software applications. This is because ___A___<sup>-1</sup> can only be represented with limited precision on a digital computer. Algorithms that make use of the value of ___b___ can usually obtain more accurate estimates of ___x___.

```
Closed Form  -  The definition of closed form is not exact. It means solving a problem by using functions
and mathematical operators from a given generally accepted set.

For example, an infinite sum is not usually considered closed-form. 
```



### 2.4 Linear Dependence and Span


In order for ___A___<sup>-1</sup> to exist, the equation ___Ax___ = ___b___ must have exactly one solution for every value of ___b___. It is also possible for this system of equations to have no solutions or infinitely many solutions for some value of ___b___. It is impossible, however, to have more than one but less than infinitely many solutions for a particular ___b___ . 
If both ___x___ and ___y___ are solutions, then :  ___z___ = _Alpha_ ___x___ + (1 - _Alpha_)___y___, is a solution for any real _Alpha_.

```
If b has more then one solution, there are infinite solutions.
```


To analyze how many solutions the equation has, think of the columns of ___A___ as specifying directions we can travel in from the _origin_ (the point specified by the vector of all zeros), then determine how many ways there are of reaching ___b___. In this view, each element of ___x___ specifies how far we should travel in each of those directions, with _x_<sub>_i_</sub> specifying how far to move in the direction of column _i_ :

TODO: Equation


This kind of operation is called __linear combination__. Formally, a linear combination of some set of vectors {___v___<sup>(1)</sup>, . . . ___v___<sup>(_n_)</sup>} is given by multiplying each vector ___v___<sup>(_i_)</sup> by a coresponding scalar coefficient and adding the result:

TODO: Equation

```
In simpler terms, a Linear Combination is an expression constructed by a set of terms (in this case, vectors) 
that are each multiplied by a constant (in this case, scalar) and adding the result:

(scalar)(vector 1) + (scalar)(vector 2) + . . . + (scalar)(vector n)

```
 
The __span__ of a set of vectors is the set of all points obtainable by linear combination of the original vectors.

```
The set of all linear combinations of a set of vectors is called the span. 

This is written as Span(v1, v2, v3), if we have a set of vectors v1, v2 and v3. 

In the linear combination, we can multiply each to any real number `c` : c1v1 + c2v2 + c3v3. 
`c` can be any real number and does not need to be the same for all vectors.
Span(v1, v2, v3) is the sum of all linear combinations for every number `c`.
```

Determining weather ___Ax___ = ___b___ has a solution amounts therefore to testing weather ___b___ is in the span of the columns of ___A___. This particular span is known as the __column space__, or the __range__ of ___A___. 



To find a solution for all values of ___b___ in _R_<sup>_m_</sup> means that the column space of ___A___ needs to be all of _R_<sup>_m_</sup>. If any point is excluded, that is a potential value of ___b___ that has no solution. 

This requirement implies that ___A___ must have at least _m_ columns, that is, _n_ >= _m_, otherwise the dimensionality of the column space would be less than _m_. As an example, consider a 3 x 2 matrix. The target ___b___ is 3D, but ___x___ is only 2D. This means that modifying the value of ___x___ at best enables us to trace out a 2D plane within _R_<sup>3</sup>. The equation has a solution only if ___b___ lies on that plane. 


This is only one requirement. There are other conditions. For example, it is possible for the columns to be redundant. Consider a 2x2 matrix where both of the columns are identical. This would have the same column space as a 2x1 matrix containing only one copy of the column. The column space is still just a line and fails to encompass all of _R_<sup>2</sup>, even though there are two columns. 

Formally, this kind of redundancy is known as __linear dependence__. A set of vectors is __linearly independent__ if no vectors in the set is a linear combination of the other vectors. If one is, it adds nothing to the set's span. What this means is that for the column space of the vector to encompass all of _R_<sup>(_m_)</sup>, the matrix must contain at least one set of _m_ linearly independent columns. This condition is sufficient for the equation to have a solution for every value of ___b___. 

Note that the set must contain exactly _m_ linearly independent columns, not at least _m_. No set of _m_-dimentional vectors can have more than _m_ mutually independent columns, but a matrix with more than _m_ columns may have more than one such set.

For the martix to have an invers, we must also ensure that the equation ___Ax___ = ___b___ has _at most_ one solution for each value of ___b___. To do so, we must make certain that the martix has at most _m_ columns. Otherwise, there is more than one way of parametrizing each solution. 


This all means that the marix must be _square_, _m_ = _n_, and that all the columns be linearly independent. 
A square martix with linearly dependent columns is known as a __singular__. If ___A___ is not square or if it is a singular, solving the equation is still possible, but we cannot use the method of matrix inversion to find the solution. 


So far we have discussed martix inverses as being multiplied on the left. It is also possible to define an inverse that is multiplied on the right: ___AA___<sup>-1</sup> = ___I___. For square martices, the left inverse and right inverse are equal. 




### 2.5 Norms


Sometimes we need to measure the size of a vector. In machine learning, this is usually done using a function called a __norm__. Formally, the _L_<sup>_p_</sup> norm is given by the equation:

TODO: Photo of norm equation


for _p_ in _R_,  _p_ >= 1

```
|x|     - the distance from the origin to `x`.
|x - y| - distance from `x` to `y`.
||x||   - the norm of the vector `x`
```

Simply put, the norm of a vector ___x___ measures the distance from the origin to the point ___x___. A norm is any function _f_ that satisfies the following properties:
* _f_(___x___) = 0,   meaning   x = 0
* _f_(___x___ + ___y___) <= _f_(___x___) + _f_(___y___) (the __triangle inequality__)
* for all _Alpha_ in _R_,  _f_(_Alpha_ * ___x___) = |_Alpha_| _f_(___x___)


The _L_<sup>2</sup> norm, with _p_ = 2 is known as the __Euclidean norm__, which is simply the Euclidean distance from the origin to the point where ___x___ is. This norm is used very frequently in machine learning and is denoted simply as ||x||, with no subscript 2.  It is also common to measure the size of a vector using the _squared_ _L_<sup>2</sup> norm, which can be calculated simply as ___x___<sup>T</sup>___x___.

```
Euclidean Distance - the length of a line segment between the two points.
```


Computationally and mathematically, it is easier to work with the squared _L_<sup>2</sup> norm as supposed to the simple _L_<sup>2</sup>. For example, each derivative of the squared _L_<sup>2</sup> with respect to each element of ___x___ depends only on the specific element of ___x___, while all the derivatives of the _L_<sup>2</sup> norm depend on the entire vector. 

Despite of this, in many cases the squared _L_<sup>2</sup> norm may be undesirable because it increses very slowly near the origin. In machine learning applications, it is important to discriminate between elements that are exactly 0 and elements that are small but not 0. In these cases, we use a function that grows at the same rate in all locations and has the same simplcity as _L_<sup>2</sup>,  _L_<sup>1</sup> norm.

The _L_<sup>1</sup> norm can be simplified to :

TODO: Equation of L1


As mentioned before, this norm is used in cases where we must differenciate between elements very close to 0 and 0. Every time an element of ___x___ moves away from 0 by an amount, _L_<sup>1</sup> norm increases by that same amount. 


We sometimes refer to the size of a vector by counting the non-zero elements it contains. Some people refer to this as _L_<sup>0</sup> norm. This is wrong. A number of entries is not a norm because it does not increse if we scale the vector by any amount. The _L_<sup>1</sup> norm is often used as a substitute for the number of non-zero entries. 


One other norm that is common in machine learning is the _L_<sup>infinity</sup> norm, also known as the __max norm__. This norm simplifies to the absolute value of the element with the largest magnitude in the vector:

TODO: infinity equation.


Sometimes we also wish to measure the size of a matrix. In deep learning, the most common way to do this is with the otherwise obscure __Frobenius norm__:

TODO: Frobenius norm


This is analogous to the _L_<sup>2</sup> norm of a vector. 


The dot product of two vectors can also be rewritten in terms of the norms : ___x___<sup>T</sup> = ||x||<sub>2</sub>||y||<sub>2</sub>cosO, where O is the angle between ___x___ and ___y___.



### 2.6 Special Kinds of Matrices and Vectors


Some special kinds of matrices and vectors have proven themselves to be very useful.

__Diagonal__ matrices consist mostly of zeros and have non-zero entries only along the main diagonal. Formally, a matrix ___D___ is diagonal only if _D_<sub>_i,j_</sub> = 0 for all _i_ != _j_. The identity matrix is a good example of a diagonal matrix. We denote a square diagonal matrix, who's diagnoal entries are given by the vector ___v___ as : diag(___v___). 

Diagonal matrices are of interest because multiplying by them is very computationally efficient. To compute diag(___v___)___x___, we only need to scale each element _x_<sub>_i_</sub> by _v_<sub>_i_</sub>. In other words, diag(___v___)___x___ is the _Hadamard product_ (or element-wise product) of ___v___ and ___x___. 


Inverting a square diagonal martix is also very efficient. The inverting exists only if every diagonal entry is non-zero. In that case, diag(___v___)<sup>-1</sup> - diag([1/_v_<sub>1</sub>, ..., 1/_v_<sub>_n_</sub>]<sup>T</sup>). 

We may derive some general machine learning algorithm in terms of arbitrary matrices but obtain a less expensive (and less descriptive) algorithm by restrictiong some matrices to be diagnoal.


Not all diagnoal matrices need to be square, but non-square diagnoal matrices do not have an inverse. We can still multiply them cheaply. For a non-square diagonal matrix ___D___, the product ___Dx___ will involve scaling every element of ___x___ and, if ___D___ is taller than it is wide, concatinate some zeros to the result, or discard some of the last elements of the vector if ___D___ is wider than tall.



A __symmetric__ matrix is any matrix that is equal to it's own transpose : ___A___ = ___A___<sup>T</sup>.

Symmetric matrices often arise when the entries are generated by some function of two arguments that is not dependent on the order of the arguments. For example, if ___A___ is a matrix of distance measurements, with ___A___<sub>_i,j_</sub> giving the distance between the point _i_ to the point _j_, then ___A___<sub>_i,j_</sub> = ___A___<sub>_j,i_</sub> because distance functions are symmetric. 



A __unit vector__ is a vector with __unit norm__ : ||___X___||<sub>2</sub> = 1
```
Unit norm : for each point 'x', ||x|| = 1.
Meaning, a vector where all the values have L^2 equal to 1.
```


Two vectors, ___x___ and ___y___ are __orthogonal__ to each other if ___x___<sup>T</sup>___y___ = 0. If both vectors have non-zero norm, this means that they are at a 90 degree angle to each other. In _R_<sup>_n_</sup>, only _n_ vectors may be mutually orthogonal with non-zero norm. We call the vectors that are orthogonal and have non-zero norm __orthonormal__.


An __orthogonal matrix__ is a square matrix whose rows are mutually orthonormal and whose columns are mutually orthonormal :
___A___<sup>T</sup>___A___ = ___AA___<sup>T</sup> = ___I___.

This implies that:
___A___<sup>-1</sup> = ___A___<sup>T</sup>


They are of interest because it is very cheap to compute the inverse. Pay attention to the fact that their rows are not merely orthogonal but fully _orthonormal_. 




### 2.7 Eigendecomposition


Many mathematical objects can be understood better by breaking them into constituent parts , or finding some properties of them that are universal, not caused by the way we choose to represent them. 

An example of this can be how integers decompose into prime factors. The way we represent the number 12 changes depending on weather we write it in base 10 or in binary, but it will always be true that 12 = 2 * 2 * 3. From this representation, we can conclude useful properties, like the fact that 12 is not divisible by 5, and that many multiples of 12 will be divisible by 3. 

In the same way, we can decompose matrices in ways that show us information about their functional properties that is not obvious from their representation as an array of elements. 

One of the most used methods of matric decomposition is called __eigendecomposition__ in which we decompose a matrix into a set of __eingenvectors__ annd __eigenvalues__.



An __eigenvector__ of a square matrix ___A___ is a non-zero vector ___v___ such that multiplication by ___A___ alters only the scale of ___v___:

___Av___ = Lambda * ___v___.

```
The symbol of Lambda (which I cannot put here) is the Half-Life logo symbol.

What is ment here by saying "alters only the scale of v" is that, any result you get when multiplying 
the eigenvector to the original matrix is a multiple of the eigenvector.

When multiplying the vector by the matrix, the direction of the vector doesn't change.
```


The scalar _Lambda_ is knows as the __eigenvalue__ corresponding to this eigenvector. (It is also possible to find a left eigenvector such that ___v___<sup>T</sup>___A___ = Lambda ___v___<sup>T</sup>).

```
How to find the eigenvalue and eigenvector:

It all starts from the equation:
> Ax = Lambda * x.

First, we have to find the eigenvalue. This starts by inputing an identity matrix 
so that we are dealing with matrix vs matrix:

> Av = Lambda * Iv

> Av - Lambda * Iv = 0


We can solve for Lambda using the determinant:

> det(A - Lambda * Iv) = 0


> det( [-6   3		   [ 1  0   
        4   5]  -  Lambda *   0  1])    =   0


> det( [ -6-Lambda      3
            4	   5-Lambda] )  = 0

> (-6-Lambda)(5-Lambda) - 3 * 4 = 0


> Lambda^2 + Lambda - 42 = 0


Solving this gets us to the answer:

> Lambda = -7 and 6


Once you have the eigenvalue, you can solve the main equation for the eigenvector. 
```
```
Very roughly, the eigenvalues of a matrix is a measure of the distortion induced by the transformation 
and the eigenvector tells you how the distortion is oriented.
```

If ___v___ is an eigenvector of ___A___, so is any rescale of the vector _s_ ___v___ for _s_ in _R_, _s_ != 0. Moreover, _s_ ___v___ still has the same eigenvalue. For this reason, we usually look only for unit eigenvectors.
```
Unit Eigenvector - ?
```


Suppose that a matrix ___A___ has _n_ linearly independent eigenvectors {___v___<sup>(1)</sup>, ..., ___v___<sup>(_n_)</sup>} with corresponding eigenvalues {Lambda<sup>(1)</sup>, ..., Lambda<sup>(_n_)</sup>}. We can concatinate all the eigenvectors into a matrix ___V___ with one eigenvector per column: ___V___ = [___v___<sup>(1)</sup>, ..., ___v___<sup>(_n_)</sup>]. Likewise, we can concatinate all the eigenvalues to form a vector _Lambda_ = [Lambda<sup>(1)</sup>, ..., Lambda<sup>(_n_)</sup>]. The __eigendecomposition__ of ___A___ is then given by:

___A___ = ___V___ diag(_Lambda_)___V___<sup>-1</sup>

```
Matrix multiplication is not commutative, therefore V and V^-1 do not cancel out.
```

Constructing matrices with specific eigenvalues and eigenvectors enables us to stretch space in desired directions. Yet we often want to _decompose_ matrices into eigenvalues and eigenvectors. Doing this helps us analyze certain properties of the martix. 
 
Not all matrices can be decomposed into eigenvalues and eigenvectors. In some cases, the decomposition exists, but it involves complex rather than real numbers. In this book we mostly look at a specific class of matrices that have a simple decomposition. Specifically, every real symmetric matrix can be decomposed into an expression using only real-valued eigenvectors and eigenvalues:

___A___ = ___Q * Lambda *Q___<sup>T</sup>

___Q___ is an orthogonal matrix composed of eigenvectors of ___A___. _Lambda_ is a diagonal matrix. The eigenvalue from _Lambda_<sub>_i,j_</sub> is associated with the eigenvalue in column _i_ of ___Q___, denoted as ___Q___<sub> : ,i </sub>. Because ___Q___ is an orthogonal matrix, we can think of ___A___ as scaling space by _Lambda_<sub>_i_</sub> in direction ___v___<sup>(_i_)</sup>.

TODO: Figure 2.3

```
Unit Vector - a vector of length 1.
In the first image, we see two simple vectors of length 1. 
In the second image, we can see the distortion induced by multiplying the matrix A. 
It scales space in direction v^(i) by Lambda.
```

While any real symmetric matrix ___A___ is guaranteed to have a eigendecomposition, it may not be unique. If any two or more eigenvectors share the same eigenvalue, then any set of orthogonal vectors lying in their span are also eigenvectors with that same eigenvalue, and we could equivalently choose a ___Q___ using those eisenvectors instead. By convention, we usually sort the entries of _Lambda_ in descending order. Under this convention, the eigendecomposition is unique only if all the eigenvalues are unique.

The eigendecomposition tells us a lot of useful facts about the matrix. The matrix is only singular if and only if any of the eigenvalues are zero. The eigendecomposition of a real symmetric matrix can also be used to optimise quadratic expressions of the form _f_(___x___) = ___x___<sup>T</sup>___Ax___ subject to ||___x___||<sub>2</sub> = 1. Whenever ___x___ is equal to an eigenvalue of ___a___, _f_ takes on the value of the corresponding eigenvalue. The maximum value of _f_ within the constraint region is the maximum eigenvalue and its minumum value within the constraint region is the minumum eigenvalue.


A matrix whose eigenvalues are all positive is called __positive definite__. A matrix whose eigenvalues are all positive or zero is called __positive semidefinite__. Likewise, if all eigenvalues are negative it is called __negative definite__ and if there are also zeros, it is called __negative semidefinite__. 

Positive semidefinite matrices are interesting because they guarantee that for all ___x___, ___x___<sup>T</sup>___Ax___ >= 0. Positive definite matrices aditionally guarantee that ___x___<sup>T</sup>___Ax___ = 0, meaning ___x___ = 0.





### 2.8 Singular Value Decomposition   

Another way to decompose a matrix is by __singular value decomposition__ (SVD). In this method, the matrix is decomposed into __singular vectors__ and __singular values__. SVD allows us to descover the same information revealed by eigendecomposition, but it is more widely applicable. Every real matrix has a SVD, the same is not true for eigendecomposition. For example, if a matrix is not square, the eigendecomposition is not defined and we must use SVD.

 
Eigendecomposition entails analyzing a matrix ___A___ to descover a matrix ___V___ of eigenvectors and a vector of eigenvalues _Lambda_ such that: ___A___ = ___V___ diag(_Lambda_)___V___<sup>-1</sup>.


The single value decomposition is similar, except this time we will rewrite ___A___ as the product of three matrices:

___A___ = ___UDV___<sup>T</sup>


Suppose ___A___ is an (_m_ x _n_) matrix. ___U___ is an (_m_ x _m_) matrix, ___D___ is an (_m_ x _n_) matrix and ___V___ is an (_n_ x _n_) matrix. ___U___ and ___V___ are both orthogonal matrices. ___D___ is a diagonal matrix, not necesarily square. 

The elements along the diagonal of ___D___ are known as __singular values__ of  the matrix ___A___. The columns of ___U___ are known as __left-singular vectors__ and the columns of ___V___ as the __right-singular vectors__.



We can actually interpret the SVD of ___A___ in terms of the eigendecomposition of functions of ___A___. The left-singular vectors of ___A___ are the eigenvectors of ___AA___<sup>T</sup>. The right-singular vectors are the eigenvectors of ___A___<sup>T</sup>___A___. The non-zero singular values of ___A___ are the square roots of the eigenvalues of ___A___<sup>T</sup>___A___. The same is true for ___AA___<sup>T</sup>.

Perhaps the most useful feature of the SVD is that we can use it to partially generalize matrix inversion to non-square matrices. 
This is explained in the next section.



### 2.9 The Moore-Penrose Pseudoinverse

Matrix inversion is not defined for matrices that are not square.

Suppose we want to make a left-inverse ___B___ of a matrix ___A___ so we can solve the linear equation ___Ax___ = ___y___ by left-multiplying each side to obtain : ___x___ = ___By___.
```
Because B is the inverse of A, it cancels out.
```

Depending on the structure of the matrix, it may not be possible to design a unique mapping from ___A___ to ___B___. If ___A___ is taller than it is wide, it is possible for the equation to have no solutions. If it is wider than toller, there could be multiple possible solutions.


The __Moore-Penrose pseudoinverse__ enables us to make some headway in those cases. The pseudoinverse of ___A___ is defined as a matrix :


TODO: equation


Algorithms for computing the pseudoinverse are not based on this definition, but rather on the formula : ___A___<sup>+</sup> = ___VD___<sup>+</sup>___U___<sup>T</sup>, where ___U___, ___D___ and ___V___ are the singular value decomposition of ___A___, and the pseudoinverse ___D___<sup>+</sup> of a diagonal matrix ___D___ is obtained by taking the reciprocal of it's non-zero elements then transposing it.

```
Reciprocal is inverse : 1 / i
```

When ___A___ has more columns than rows, solving a linear equation using the pseudoinverse provides one of the many possible solutions. Specifically, it provides the solution ___X___ = ___A___<sup>+</sup>___y___ with minimal Euclidean norm ||___x___||<sub>2</sub> among all possible solutions.

```
Minimal Euclidean Norm - ?
```

When ___A___ had more rows than columns, it is possible to have no solution. In this case, the pseudoinverse gives us the ___x___ for which ___Ax___ is as close as possible to ___y___ in terms of Euclidean norm : ||___Ax___ - ___y___||<sub>2</sub>.


### 2.10 The Trace Operator


The trace operator gives the sum of all the diagonal entries of a matrix:

TODO: equation


It is useful for many reasons. Some operations that are difficult to specify without resorting to summation notation can be specified using matrix products and the trace operator. 
```
Summation Notation - Sigma operator
```

For example, the trace operator offers an alternative way of writing the _Frebenius norm_ of a matrix:

TODO: norm

Writing an expression using the trace operator lets us manipulate the expression using many different identities. For example, the operator is invatiant to the transpose operator :

Tr(___A___) = Tr(___A___<sup>T</sup>).

The trace of a square matrix composed of many factors is also invariant to moving the last factor into the first position, if the shapes of the corresponding matrices allow the resulting product to be defined:

Tr(___ABC___) = Tr(___CAB___) = Tr(___BCA___)

or more generally,

TODO: equation


This invariance to cyclic permutations holds even if the resulting product has a different shape. For example, for ___A___ in _R_<sup>_m_ x _n_</sup> and ___B___ in _R_<sup>_n_ x _m_</sup>, we have: Tr(___AB___) = Tr(___BA___) even though ___AB___ is in _R_<sup>_m_ x _m_</sup> and ___BA___ is in _R_<sup>_n_ x _n_</sup>.

```
Permutation - Arrangement of a set into a sequence or Rearranement.

Cyclic Permutations - Permutation of a set amount, moving the last items to the front.
It refers to the example just before. 
```

Another important aspect is that a scalar it it's own trace: _a_ = Tr(_a_).



### 2.11 The Determinant

The determinant of a square matrix, shown as det(___A___) is a function that maps matrices to real scalars. The determinant is equal to the product of all eigenvalues of the matrix. The absolute value of the determinant can be thought of as a measure of how much multiplication by the matrix expands or contracts space. If the determinant is 0, then space is contracted completely at least on one dimension, causing it to lose all its volume. If the determinant is 1, then the transformation preserves volume.

```
TODO: A more intuitive explanation
```




