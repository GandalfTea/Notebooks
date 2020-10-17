
# 1.3 Formulating Abstractions with Higher-Order Procedures :

Higher-Order Abstractions -- abstractions that take as arguments other abstractions.



#1.29
Simpson's Rule :

h/3 (y0 + 4y1 + 2y2 + 4y3 + 2y4 + 4y5 + ... + 4yn-1 + +yn) 

Fab = [ ((a+b)/3) * (f(a + 0 * h) + 4f(a +1h) + 2f(a+2h) ... 2f(a + (n-2)h) + 4f(a + (n-1)h) + f(n)) ]


h = (b - a) / n
yk = f(a + kh).
arguments -- f, a ,b;

-----------------------------------------------------------------

//not tried and does not look like most answers.

(define (simpson f a b n)
   (define h (/ (- b a) n))
   (define (simpson-iter x)
       (if (< x b)  //b is inherited.
	   (cond ((even? x) (+ (* 4 ( f(+ a (* x h)))) 
	    	               (simpson-iter (f a (+ x 1)))))
	    (else (+ (* 2 ( f(+ a(* 2 h))))
		  (simpson-iter (f a (+ x 1))))))))
   (times (/ h 3) (+ (simpson-iter(0) f(b)))))



#1.30
From recursion to iterative:

original :

(define (sum term a next b)
    (if (> a b)
	0
	(+ (term a)
	   (sum term (next a) next b))))
-------------------------------------------------------------------

(define (sum term a next b)
    (define (iter a result)
	(if (> a b)
	     0
	     (iter (- a 1) (+ result a) )))
    (iter (a 0) ))


