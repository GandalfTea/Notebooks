

1.3.3 Peocedures as General Methods 
-------------------------------------------------------

# Finding roots of equations by the half-interval method


if f(a) < 0 < f(b),
f must have at least one zero between a and b.

x = average a b
if f(x) > 0, than f must have a 0 between a and x.
if f(x) < 0, than f must have a 0 between x and b.

O(log(L/T)) where L is the leanght of the original interval and T is error tolerance.


> (define (search f neg-point pos-point)
      (let ((midpoint (average neg-point pos-point)))
      (if (close-enough? neg-point pos-point)
	   midpoint
	   (let ((test-value (f midpoint )))
	       (cond ((positive? test-value)
		      (search f neg-point midpoint))
		     ((negative? test-value)
		      (search f midpoint pos-point))
		     (else midpoint))))))

> (define (close-enough? x y) (< abs (- x y) 0.001))

To test the input:

> (define (half-interval-method f a b)
      (let ((a-value (f a))
	    (b-value (f b)))
	(cond ((and (negative? a-value) (positive? b-value))
	       (search f a b))
	      ((and (negative? b-value) (positive? a-value))
	       (search f b a))
	      (else (error "Values are not of opposite sign" a b))))) 




# Finding fixed points of functions


A number x is called a fixed point of a function if x satisfies the equasion f(x) = x


For some functions, we can locate a fixed point by beginning with an initial guess and applying f repetedly:

f(x), f(f(x)), f(f(f(x))), ...

> (define tolerance 0.00001)
> (define (fixed-point f first-guess)
     (define (close-enough? v1 v2)
	(< (abs (- v1 v2))
	   tolerance))
     (define (try guess)
	(let ((next (f guess)))
	   (if ((close-enough? guess next)
	        next
	   	(try next)))))
     (try first-guess))


We can use this method to write a square root function : 

> (define (sqrt x)
      (fixed-point (lambda (y) (/ x y)) 1.0))

This results in a loop and therefore is not good. Can be fixed like this :

> (define (sqrt x)
      (fixed-point (lambda (y) (average y (/ x y))) 1.0))



# Average Dumping : averaging successive approximations to a solution.












