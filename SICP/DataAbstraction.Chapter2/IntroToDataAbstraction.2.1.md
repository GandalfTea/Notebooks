


## 2.1 Introduction to DATA ABSTRACTION :


Data abstraction is a methodology that enables us to isolate how a compound data object is used from the details of how it is constructed from more primitive data objects.

The basic ideea is to structure the program in such a way that it can make use of abstract data, without details of it's tipe. At the same time, a concrete data representation is defined independent of the programs that use the data. The interface between them will be a set of procedures, called selectors and constructors.



### Pairs

To enable data abstraction, the language provedes a compound structure called a pair witch can be constructed using the primitive procedure > cons. This procedure takes two arguments and returns a compound data object that contains the two arguments as parts. We can extract the parts using the procedures > car and > cdr. 
```
> (define x (cons 1 2))
  (car x)
  1
  (cdr x)
  2
```
Cons can be used to make pairs whose elements are pairs :
```
> (define x (cons 1 2))
  (define y (cons 3 4))
  (define z (cons x y))
  (car (car z))
  1
  (car (cdr z))
  3
```

### 2.1.1 Example : Rational numbers :

> 1.5 is rational because it is 3 / 2.
rational number = number that can be written as a function of 2 integers, a numerator and denominator.
Almost all numbers are rational.


(make-rational n d) returns the rational number whose numerator is n and denominator is d :
```
> (define (make-rat n d) (cons n d)) witch can be written as :
> (define make-rat  cons) // in this case make-rat is another name for the same procedure.
```
(numer x) returns the numerator of x :
```
> (define (numer x) (car x)  OR 
> (define numer car)
```
(denom x) returns the denominator of x :
```
> (define (denom x) (cdr x)) OR
> (define denom cdr)
```
To display the number :
```
> (define (print-rat x)
    (newline)
    (display (numer x))
    (display "/")
    (display (denom x)))
```


Furthermore, we can use the rational numbers in calculations :


#### ADDITION :
```
> (define (add-rat x y)
     (make-rat (+ (* (numer x) (denom y))
		  (* (numer y) (denom x)))
	       (* (denom x) (denom y))))
```
#### SUBTRACTION :
```
> (define (sub-rat x y)
     (make-rat (- (* (numer x) (denom y))
		  (* (numer y) (denom x)))
	       (* (denom x) (denom y))))
```
#### MULTIPLICATION :
```
> (define (mul-rat x y)
     (make-rat (* (numer x) (numer y))
	       (* (denom x) (denom y))))
```
#### DIVISION :
```
> (define (div-rat x y)
     (make-rat (* (numer x) (denom y))
	       (* (denom x) (numer y))))
```
#### EQUAL :
```
> (define (equal-rat? x y)
     (= (* (numer x) (denom y))
	(* (numer y) (denom x))))
```

Example :
```
> (define one-half (make-rat 1 2))
> (define one-third (make-rat 1 3))

> (print-rat (mul-rat one-half one-third))
  5 / 6
> (print-rat (add-rat one-third one-third))
  6 / 9
```
As the examples show, out implementation does not reduce rational numbers to lowest term.

We can use gdc (gratest common devisor) from earlier to make it do that :
```
> (define (make-rat n d)
     (let ((g (gdc n d))))
	(cons (/ n g) (/ d g)))

> (print-rat (add-rat one-third one-third))
  2 / 3
```









