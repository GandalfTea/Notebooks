
# lambda

It is used instead of defineing a proocedure inside another procedure:
It is equivalent to a define but has no name.

> (lambda (x) (+ x 4))

> (define (pi-sum a b)
      (sum (lambda (x) (/ 1.0 (* x (+ x 2))))
	    a
	   (lambda (x) (+ x 4))
	    b))

> (define (integral f a b dx)
    (* (sum f
	    (+ a (/dc 2.0))
	    (lambda (x) (+ x dx))
	     b)
       dx))


# let

We can use lambda to create local variables :

> f(x,y) = x(1 + xy)^2 + y(1 - y) + (1 + xy)(1 - y)

can also be written as :

> a = 1 + xy.
> b = 1 - y.
> f(x y) = xa^2 + yb + ab.

in lisp can be written as:

> (define (f x y)
     ((lambda (a b)
        (+ (* x (square a))
           (* y b)
	   (* a b)))
      (+ 1 (* x y))
      (- 1 y)))


This is so usefull that the procedure let was created :
let is syntactic sugar version of lambda.

> (let ( (variable1 (expresion1)) (variable2 (expresion 2)) )
       ( body )

TODO: let as a lambda?

> (define (f x y)
     (let ((a (+ 1 (* x y)))
	   (b (- 1 y)))
       (+ (* x (square a))
	  (* y b)
	  (* a b))))


The scope of a variable created by let is the body of the let :


> x = 5
> (+ (let ((x 3))		// x is 3 in this scope only
	(+ x (* x 10)))		= 38
      x)


The value of the variables is computed outside of the let :

> x = 2
> (let ((x 3)
	(y (+ x 2)))	//value of y is outer x + 2 = 4
    (* x y))		// = 12




