

# 2.1.4 Exercise : Interval Arithmetic :


Parallel equivalent resistance of two resistors :  Rp = 1 / 1 / R1 + 1 / R2

Resistance values have a specified tolerance :
6.8 ohms with 10% tolerance means that
the lower tolerance is 6.8 - 0.68 = 6.12
the upper tolerance is 6.8 + 0.68 = 7.48

If you have a resistor with 6.8 ohms 10% and one 4.7 ohms 5%, the resistance using Rp is between 2.58 ohms and 2.97 ohms

Interval - abstract data

-----------------------------------------------------------------------------

> (define make-interval cons)
  (define lower-bound car)
  (define upper-bound cdr)

> (define (add-interval x y)
	(make-interval (+ (lower-bound x) (lower-bound y))
		       (+ (upper-bound x) (upper-bound y))))


// MIN and MAX are primitives that take any number of arguments.
> (define (mul-interval x y)
	(let ((p1 (* (lower-bound x) (lower-bound y)))
	      (p2 (* (lower-bound x) (upper-bound y)))
	      (p3 (* (upper-bound x) (lower-bound y)))
	      (p4 (* (upper-bound x) (upper-bound y))))
	(make-interval (min p1 p2 p3 p4)
		       (max p1 p2 p3 p4))))

Reciprocal of a is 1 / a.

> (define (div-interval x y)
	((let a (make-interval (/ 1.0 (upper-bound y))
			       (/ 1.0 (lower-bound y))))
	        (if (= a 0) (error "Interval is 0")
			    (mul-interval x a))))



# 2.8


> (define (sub-interval x y)
	(make-interval (abs (- (lower-bound x) (lower-bound y)))
		       (abs (- (upper-bound x) (upper-bound y)))))



# 2.9

Width of interval = 2 / (abs (upper - lower))
width is a measure of uncertenty

for some operation, the width of combining intervals  = sum of width of argument intervals
for some not

show that :
width of add-interval or sub-interval is = to add-width of the arguments.
width of mul and div is not.

-------------------------------------------------------------------

> (devine (width x)
	(/ 2 (abs (- ((upper-bound x) - (lower-bound x))))))

intervals :
x = 5, 8
y = 2, 6

> (define a (add-interval x y))
(7, 14)

> (width a)
3.5

> (width x)
1.5

> (width y)
2


//true
width x + width y = width a


// no good with multiplication or division
> (define a (mul-interval x y))
(10, 48)
> (width a)
19






