

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
It is calculated as the set of all possible ordered pairs with the first element from X (first set) and the second from Y (second set).

X * Y = {(x,y): x is in X and y is in Y}

TODO: Photo from wikipedia

In this case, the vector lies in the set R^2 because there are two vectors, X and Y.

```
When we need to explicitly identify the elements, we write them as a column like so:

TODO: Photo

We can think of vectors as identifying points in space, with each element giving the cooridinate along a different axis. 

Sometimes we need to index a set of elements of a vector. In this case, we define a set containing the indices and write the set as a subscript. For example, to access x<sub>1</sub>, x<sub>3</sub> and x<sub>6</sub>, we define the set _S_ = {1,3,6} and write ___x___<sub>S</sub>. 






