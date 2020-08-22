
1.3.4 Procedures as return values :
--------------------------------------------------


# Comparing sqrt procedures with and without abstractions :

original :
 
> (define (sqrt-iter guess x)
     (if (good-enough? guess x)
	  guess
	 (sqrt-iter (improve guess x) x)))

> (define (improve guess x)
     (average guess (/ x guess)))

> (define (good-enough? guess x)
     (< (abs (- (square guess) x)) 0.001))

> (define (sqrt x)
     (sqrt-iter 1.0 x))


with abstractions :

> (define tolerance 0.000001)
> (define (fixed-point f first-guess)
     (define (close-enough? v1 v2)
	(< (abs (- v1 v2)) tolerance))
     (define (try guess)
	(let ((next (f guess)))
	    (if (close-enough? guess next)
		 next
		(try next))))
     (try first-guess))

> (define (average-damp f)
     (lambda (x) (average x (f x))))

> (define (sqrt x)
     (fixed-point (average-damp (lambda (y) (/ x y)))
		  1.0))










