

## Linear Algebra
### Chapter 2


### 2.1 Scalats, vectors, Matrices and Tensors

The study of linear algebra involves several types of mathematical objects:

* ___Scalars___ are comprised of just a single number (in contrast to other objects in LA). They are written in italics. When defined, it is specified wahat kind of numbers they are : R - real number, N - natural number.
```
R - Real Numbers are numbers that can represent a distance along a line. 
They contain all rational numbers, such as integers (-5), fraction (4/3)
and irrational numbers (sqrt(2)). 

N - Natural Numbers are numbers that are positive, often used for counting. 
 Controversy surrounds the ideea if it includes 0 or not.
```

* ___Vectors___ are comprised from an array of numbers. The numbers are arranged in order. We identify each number by it's index in that order. The first element of _x_ is _x_<sup>1</sup>, second is _x_<sup>2</sup>, etc. We must also specify what kind of numbers they are. If each element is in _R_, and the vector has _n_ elements, then the vector lies in the set formed by taking the Cartesian product of _R_ _n_ times, denoted as _R<sup>n</sup>_. 
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


A scalar is esentially a mmatrix with a single entry. From this, we can see that the transpose of a scalar itself: _a_ = _a_<sup>T</sup>.

We can add matrices to each other as long as they have the same shape by adding the coresponding elements : ___C___ = ___A___ + ___B___ where _C_<sub>_i,j_</sub> = _A_<sub>_i,j_</sub> + _B_<sub>_i,j_</sub>.

We can also add a scalar to a martix, or multiply a matric by a scalar. This is done by performing the operation on each element of the matrix: ___D___ = _a_ * ___B___ + _c_ where _D_<sub>_i,j_</sub> = _a_ * _B_<sub>_i,j_</sub> + _c_.


In the context of deep learning we will also use some less conventional notation. We allow the addition of a matric and a vector, resulting in another matrix: ___C___ = ___A___ + ___b___ where ___C___<sub>_i,j_</sub> = _A_<sub>_i,j_</sub> + _b_<sub>_j_</sub>. This is done by adding the vector to each row of the matrix. This eliminated the need to define a matrix wit ___b___ copied into each row during addition. The name of this operation, the implicit copying of ___b___ to many locations is called __broadcasting__. 


### 2.2 Multiplying Matrices and Vectors







