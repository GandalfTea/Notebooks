

## Linear Algebra
### Chapter 2


### 2.1 Scalars, vectors, Matrices and Tensors

The study of linear algebra involves several types of mathematical objects:

* ___Scalars___ are comprised of just a single number (in contrast to other objects in LA). They are written in italics. When defined, it is specified wahat kind of numbers they are : R - real number, N - natural number.
```
R - Real Numbers are numbers that can represent a distance along a line. 
They contain all rational numbers, such as integers (-5), fraction (4/3)
and irrational numbers (sqrt(2)). 

N - Natural Numbers are numbers that are positive, often used for counting. 
 Controversy surrounds the ideea if it includes 0 or not.
```

* ___Vectors___ are comprised from an array of numbers. The numbers are arranged in order. We identify each number by it's index in that order. The first element of _x_ is _x_<sub>1</sub>, second is _x_<sub>2</sub>, etc. We must also specify what kind of numbers they are. If each element is in _R_, and the vector has _n_ elements, then the vector lies in the set formed by taking the Cartesian product of _R_ _n_ times, denoted as _R<sup>n</sup>_. 
```
Cartesian Product - It is a product between two sets of numbers (arrays).
It is calculated as the set of all possible ordered pairs with the first
element from X (first set) and the second from Y (second set).

X * Y = {(x,y): x is in X and y is in Y}

TODO: Photo from wikipedia

In this case, the vector lies in the set R^2 because there are two vectors, X and Y.

```
When we need to explicitly identify the elements, we write them as a column like so:

TODO: Photo

We can think of vectors as identifying points in space, with each element giving the cooridinate along a different axis. 

Sometimes we need to index a set of elements of a vector. In this case, we define a set containing the indices and write the set as a subscript. For example, to access x<sub>1</sub>, x<sub>3</sub> and x<sub>6</sub>, we define the set _S_ = {1,3,6} and write ___x___<sub>S</sub>. We use the - sign to index the complement of a set. For example, ___x___<sub>-1</sub> is the vector containing all elements of ___x___ except for _x_<sub>1</sub>. Likewise, ___x___<sub>-S</sub> is a vector containing all elements of ___x___ except for the elements in _S_.


* ___Matrices___ are a 2D array of numbers. Each element has two indeces instead of one. They are usually named using uppercase bold typeface, such as ___A___. If a real-valued matrix (consisting only of real numbers) ___A___ has a height of _m_ and a width of _n_, we say that ___A___ is in _R_<sup>_m_ * _n_</sup>. 
We idenify elements with subscript, like this :
_A_<sub>1,1</sub>, _A_<sub>m,n</sub>.     
We identify all the elements in the row _i_ as _A_<sub>i,:</sub>.     
Likewise, we identify all elements in the _i_ column as _A_<sub>:,i</sub>.    

When explicitly identifying the elements of a matrix, we display them like this:

TODO: Photo

Sometimes, we need to index matric-valued expressions that are not just a single letter. We use subscript after the expression but do not convert anything to lowercase. For example _f_(___A___)<sub>i,j</sub> gives element (_i,j_) of the martic computed by applying a function _f_ to ___A___.


* ___Tensors___ are arrays of numbers arranged on a regular grid with a variable number of axes. We identify the element of ___A___ at coordinates (_i,j,k_)  by ___A___<sub>_i,j,k_</sub>.

```
All the above data types are of type tensor. That does not mean they should be reffered as such. 
A Scalar is a 0 dimensional tensor, array 1 dimmensional tensor and a matrix a 2 dimensional tensor. 
Strictly speaking we refer to a tensor as such if it has >= 3 dimensions.
```


An important operation on matrices is the __transpose__. The transpose of a matrix is te mirror image of the matrix across a diagonal line, called the __main diagonal__, running down and to the right, starting from the upper left corner. We denote the transpose of a matric ___A___ as ___A___<sup>T</sup>, as in defined as: (___A___<sup>T</sup>)<sub>_i,j_</sub> = _A_<sub>_i,j_</sub>. A graphical representation of this process is:
```
For lack of a better alternative, I will use a capital T to display the transpose symbol. 
Please know this is not usually the case.
```


TODO: Photo of matric diagonal line.



Vectors are matrices with a single column, therefore the transpose of a vector is a matrix with only one row. Sometimes we define a vector by writing it as a row matric, then use the transpose operator to turn it into a standard column vector. For example: ___x___ = [_x_<sub>1</sub>,_x_<sub>2</sub>, _x_<sub>3</sub>]<sup>T</sup>.


A scalar is esentially a mmatrix with a single entry. From this, we can see that the transpose of a is scalar itself: _a_ = _a_<sup>T</sup>.

We can add matrices to each other as long as they have the same shape by adding the coresponding elements : ___C___ = ___A___ + ___B___ where _C_<sub>_i,j_</sub> = _A_<sub>_i,j_</sub> + _B_<sub>_i,j_</sub>.

We can also add a scalar to a martix, or multiply a matric by a scalar. This is done by performing the operation on each element of the matrix: ___D___ = _a_ * ___B___ + _c_ where _D_<sub>_i,j_</sub> = _a_ * _B_<sub>_i,j_</sub> + _c_.


In the context of deep learning we will also use some less conventional notation. We allow the addition of a matric and a vector, resulting in another matrix: ___C___ = ___A___ + ___b___ where ___C___<sub>_i,j_</sub> = _A_<sub>_i,j_</sub> + _b_<sub>_j_</sub>. This is done by adding the vector to each row of the matrix. This eliminated the need to define a matrix wit ___b___ copied into each row during addition. The name of this operation, the implicit copying of ___b___ to many locations is called __broadcasting__. 


### 2.2 Multiplying Matrices and Vectors


Multiplication of matrices is one of the most important operation. When calculating the _matrix product_ of matrices ___A___ and ___B___, it results in a third matric ___C___. In order for this to happen, ___A___ must have the same number of columns as ___B___ has rows. For example, if ___A___ is of shape _m * n_ and ___B___ is of shape _n * p_, the resulting matrix ___C___ will be of shape _m * p_. We show this product like this: ___C___ = ___AB___. The product operation is defined by:

TODO: Matrix multiplication formula.


The standard product of two matrices is not just a matrix containing the product of the individual elements. Such a product is called __element-wise product__ or __Hadamard product__. 


The dot product between two vectors ___x___ and ___y___ of the same dimensionality is the matrix product ___x___<sup>T</sup>___y___. We can think of the matrix product ___C___ = ___AB___ as computing _C_<sub>_i,j_</sub> as the dot product between row _i_ of ___A___ an column _j_ of ___B___.

There are many useful properties that make mathematical analysis of matrices more conveniant. For example, matrix multiplication is distributive : ___A___(___B___+___C___) = ___AB___ + ___AC___. 


It is also associative:
___A___(___BC___) = (___AB___)___C___.

```
Associative property : Rearanging the paranthesis will not change the result.
```

Matrix multiplication is not commutative (the condition ___AB___ = ___BA___ does not always hold), unlike scalar muliplication. However, the dot product of two vectors is commutative:
___x___<sup>T</sup>___y___ = ___y___<sup>T</sup>___x___.


The transpose of a matric product has a simple form:

(___AB___)<sup>T</sup> = ___B___<sup>T</sup>___A___<sup>T</sup>

This allows us to demonstrate the equasion : ___x___<sup>T</sup>___y___ = ___y___<sup>T</sup>___x___ by exploiting the fact that the value of such a product is a scaler and therefore equal to its own transpose:


___x___<sup>T</sup>___y___ = (___x___ <sup>T</sup>___y___<sup>T</sup>)<sup>T</sup> =  ___y___<sup>T</sup>___x___.



This is enough linear algebra to write doen a system of linear equasions:

___Ax___ = ___b___

where ___A___ is a known vector and is in _R_<sup>_m_ * _n_</sup>, ___b___ is a known vector and in _R_<sup>_m_</sup> and ___x___ is a cector of unknown variables we would like to solve for in _R_<sup>_n_</sup>. Each element _x_<sub>_i_</sub> od ___x___ is one of those unknown variables. Each row of ___A___ and each element of ___b___ provide another constraint. We can rewrite the equasion from above as:
___A___<sub>1 , :</sub>___x___ = _b_<sub>1</sub>
___A___<sub>2 , :</sub>___x___ = _b_<sub>2</sub>
. . .
___A___<sub>_m_ , :</sub>___x___ = _b_<sub>_m_</sub>

Or even more explicitly as :
___A___<sub>1,1</sub>_x_<sub>1</sub> + ___A___<sub>1,2</sub>_x_<sub>2</sub> + . . . + ___A___<sub>1,_n_</sub>_x_<sub>_n_</sub> = _b_<sub>1</sub>

___A___<sub>2,1</sub>_x_<sub>1</sub> + ___A___<sub>2,2</sub>_x_<sub>2</sub> + . . . + ___A___<sub>2,_n_</sub>_x_<sub>_n_</sub> = _b_<sub>2</sub>

. . .


___A___<sub>_m_,1</sub>_x_<sub>1</sub> + ___A___<sub>_m_,2</sub>_x_<sub>2</sub> + . . . + ___A___<sub>_m_,_n_</sub>_x_<sub>_n_</sub> = _b_<sub>_m_</sub>



Matrix vector product notation provides a more compact representation for equasions of this form. 


### 2.3 Identity and Inverse Matrices

In linear algebra, there is a powerful tool called __matric inversion__ that enables us to solve the equasion : ___Ax___ = ___b___  for many values of ___A___.

In order to understand matric inversion, we first need to introduce the concept of an __identity matrix__. An identity matrix is a matrix that does not change any vector when we multply tht vector by the matrix. We show the identity matrix that preserves _n_-dimensional vectors as ___I___ <sub>_n_</sub>. Formally, ___I___ <sub>_n_</sub> is in _R_<sup>_n_ * _n_</sup>, and for all ___x___ in _R_<sup>_n_</sup>,___I___ <sub>_n_</sub>___x___ = ___x___.


The structure of the identity matrix is simple: all entries along the main diagonal are 1, while all the other entries are zero. As an example:
[ 1 0 0      
  0 1 0      
  0 0 1 ]   

The matrix inverse of ___A___ is denoted as ___A___<sup>-1</sup> and is defined as the matrix such as that ___A___<sup>-1</sup>___A___ = ___I___<sub>_n_</sub>.


We can now solve the equasion ___Ax___ = ___b___ by the following steps:

___Ax___ = ___b___     
___A___<sup>-1</sup>___Ax___ =  ___A___<sup>-1</sup>___b___     
___I___<sub>_n_</sub>___x___ =  ___A___<sup>-1</sup>___b___     
___x___ =  ___A___<sup>-1</sup>___b___.
      
This process is dependent on it being possible to find ___A___<sup>-1</sup>. We discuss the conditions for the existance of ___A___<sup>-1</sup> in the following section. 

When ___A___<sup>-1</sup> exists, many algorithms can find it in closed form. In theory, the same inverse matrix can be used to solve the equation for many different values of ___b___. It is primarely useful as a theoretical tool, and should not actually be used in practice for most software applications. This is because ___A___<sup>-1</sup> can only be represented with limited precision on a digital computer. Algorithms that make use of the value of ___b___ can usually obtain more accurate estimates of ___x___.


### 2.4 Linear Dependence and Span


In order for ___A___<sup>-1</sup> to exist, the equasion ___Ax___ = ___b___ must have exactly one solution for every value of ___b___. It is also possible for this system of equasions to have no solutions or infinitely many solutions for some value of ___b___. It is impossible, however, to have more than one but less than infinitely many solutions for a particular ___b___ ; if both ___x___ and ___y___ are solutions, then ___z___ = Alpha ___x___ + (1 - Alpha)___y___ is also a solution for any real Alpha.


To analyze how many solutions the equasion has, think of the columns of ___A___ as specifying directions we can travel in from the _origin_ (the point specified by the vector of all zeros), then determine how many ways there are of reaching ___b___. In this view, each element of ___x___ specifies how far we should travel in each of those directions, with _x_<sub>_i_</sub> specifying how far to move in te direction of column _i_ :

TODO: Equasion

This kind of operation is called __linear combination__. Formally, a linear combination of some set of vectors {___v___<sup>(1)</sup>, . . . ___v___<sup>(_n_)</sup>} is given by multiplying each vector ___v___<sup>(_i_)</sup> by a coresponding scalar coefficient and adding the result:

TODO: Equasion
 
The __span__ of a set of vectors is the set of all points obtainable by linear combination of the original vectors.

Determining weather ___Ax___ = ___b___ has a solution amounts therefore to resting weather ___b___ is in the span of the columns of ___A___. This particular span is known as the __column space__, or the __range__ of ___A___. 



To find a solution for all values of ___b___ in _R_<sup>_m_</sup> means that the column space of ___A___ be all of _R_<sup>_m_</sup>. If any point is excluded, that is a potential value of ___b___ that has no solution. This requirement implies that ___A___ must have at least _m_ columns, that is, _n_ >= _m_, otherwise the dimensionality of the column space would be less than _m_. As an example, consider a 3 x 2 matrix. The target ___b___ is 3D, but ___x___ is only 2D. This means that modifying the value of ___x___ at best enables us to trace out a 2D plane within _R_<sup>3</sup>. The equasion has a solution only if ___b___ lies on that plane. 


This is only one requirement. There are other conditions. For example, it is possible for the columns to be redundant. Consider a 2x2 matrix where both of the columns are identical. This would have the same column space as a 2x1 matrix containing only one copy of the column. The column space is still just a line and fails to encompass all of _R_<sup>2</sup>, even though there are two columns. 

Formally, this kind of redundancy is known as __linear dependence__. A set of vectors is _linearly independent_ if no vectors in the set is a lienar combination of the other vectors. If one is, it adds nothing to the set's span. What this means is that for the column space of the vector to encompass all of _R_<sup>(_m_)</sup>, the matrix must contain at least one set of _m_ linearly independent columns. Each set must contain exactly _m_ linearly independent columns, not at least _m_. No set of _m_-dimentional vectors can have more than _m_ mutually independent columns, but a matrix with more than _m_ columns may have more than one such set. This condition is sufficient for the equasion to have a solution for every value of ___b___. 


For the martix to have an invers, we mst also ensure that the equasion ___Ax___ = ___b___ has _at most_ one solution for each value of ___b___. To do so, we must make certain that the martix has at most _m_ columns. Otherwise, there is more than one wat of parametrizing each solution. 


This all means that the marix must be _square_, _m_ = _n_, and that all the columns be linearly independent. 
A square martix with linearly idependent columns is known as a __singular__. If ___A___ is not square or if it is a singular, solving te equasion is still possible, but we cannot use the method of matrix inversion to find the solution. 


So far we have discussed martix inverses as being multiplied on the left. It is also possible to define an inverse that is multiplied on the right: ___AA___<sup>-1</sup> = ___I___. For square martices, the left inverse and right inverse are equal. 




### 2.5 Norms



