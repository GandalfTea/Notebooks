

# 2.1.2 Abstraction Bariers :


We should bind primitive language in as small a program possible and build ontop of building blocks, for when the primitive language changes, we can easily re-write the necesarry code and still have everything working.

Higher level abstractions do not concern themselves with the lower level primitive language.

Proceduers at each abstraction level are the interfaces that define the abstraction barriers and connect different levels.


# Exercise 2.2

Representing lines with a starting point and an end point in a plane :

constructor make-segment,
selectors start-point and end-point.

constructor make-point
selectors x-point and y-point

mid-point? average of start and end

----------------------------------------------------------------------
// tested and proud

> (define make-point cons)
  (define x-point car)
  (define y-point cdr)

> (define start-segment x-point)
  (define end-segment y-point)

> (define (make-segment start-point-x start-point-y end-point-x end-point-y)
	(define end-point (cons end-point-x end-point-y))
	(define start-point (cons start-point-x start-point-y))
	(cons (start-point) (end-point)))

> (define (mid-point x)
	(cons (/ 2 (+ (x-point (start-segment x)) (y-point (start-segment x))))
	      (/ 2 (+ (x-point (end-segment x)) (y-point (end-segment x)))) 




