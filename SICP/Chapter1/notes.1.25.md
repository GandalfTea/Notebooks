
#1.25
#Gratest Common Divisors - GCD

Euclid's Algorithm :
GCD(a, b) = GCD(b, rest)


GCD(206, 40) = GCD(40, 6)
	     = GCD(6, 4)
	     = GCD(4, 2)
	     = GCD(2, 0)
	     = 2

In Lisp:
(define (gcd a b)
    (if (= b 0)
	a
	(gcd b (remainder a b))))




#1.26
#Example -- Testing for Primality


(define (divides? a b) (= (remainder b a) 0))

(define (find-divisor n test-divisor)
    (cond ((> (square test-divisor) n) n)
	  ((divides? test-divisor n) test-divisor)
	  (else (find-divisor n (+ test divisor 1)))))


//Test it 
(define (smallest-divisor n) (find-divisor n 2))


PRIME
(define (prime? n)
    (= n (smallest-divisor n)))






#Fermat's Little Theorem
If n is a prime number and a is any positive integer less than n, than the remainder of a to the power of n when dividedby n is the same as a divided by n.

in Lisp:


//compute the exponent of a number modulo another number
(define (expmod base exp m)
    (cond ((= exp 0) 1)
	  ((even? exp)
		(remainder (square (expmod base (/ exp 2) m)) m))
	  (else (remainder (* base (expmod base (- exp 1) m)) m))))


// Fermat Test
(define (fermat-test n)
    (define (try-it a)
	(= (expmod a n n) a))
    (try-it (+ 1 (random (- n 1)))))



//Try it a given number of times
(define (fast-prime? n time)
    (cond ((= time 0) true)
	  ((fermat-test n) (fast-prime? n (- time 1)))
	  (else false)))


#Calculate compute time example

(define (timed-prime-test n)
    (newline)
    (display n)
    (start-prime-test n (runtime)))

(define (start-prime-test n start-time)
    (if (prime? n)
	(report-prime (- (runtime) start-time))))

(define (report-time  elapsed-time)
    (display "*** ")
    (display elapsed-time))




##Exercises


#1.25

--expmod
(define (expmod base exp m)
    (cond ((= exp 0) 1)
	  ((even? exp)
		(remainder (square (expmod base (/ exp 2) m)) m))
	  (else (remainder (* base (expmod base (- exp 1) m)) m))))

	Output for expmod (5 101 101) :
		square 5
		square 24
		square 71
		square 92
		square 1
		square 1 		
		5


(define (expmod base exp m)
    (remainder (fast-expt base exp) m))
	
	Output for (expmod 5 101 101) :
		square 5
		square 25
		square 625
		square 390625
		square 152587890625
		square 23283064365386962890625
		5

-- fast-expt
(define (fast-expt base exp)
    (cond ((= exp 0) 1)
	  ((even? exp) 
		(square (fast-expt base (/ exp 2))))
	  (else (* base (fast-expt base (- exp 1))))))




#1.28

Miller-Rabin test :

n - prime number
a - any positive integer : a < n

a^(n-1) congruent 1 (mod n).
-----------------------------------
Fermat
a^n congruent a (mod n)
(define (expmod base exp m)
    (cond ((= exp 0) 1)
	  ((even? exp)
		(remainder (square (expmod base (/ exp 2) m)) m))
	  (else (remainder (* base (expmod base (- exp 1) m)) m))))


//with no recursive exponent calculation :
(define (expmod base exp m)
    (if (= exp 0)
	1
	(remainder (expt base exp)  m)))


(define (fermat-test n)
    (define (try-it a)
	(= (expmod a n n) a))
    (try-it (+ 1 (random (- n 1)))))

----------------

//TODO: check for nontrivial square root of 1 modulo n:
num =! 1 || n - 1  where num^2 = 1 % n.


(define (miller-rabin-expmod base exp m)
    (define (squaremod-with-check x)
        (define (check-nontrivial-sqrt1 x square)
	    (if (and (= square 1)
		     (not (= x 1))
		     (not (= x (- m 1))))
	      0
	      square))
    (check-nontrivia-sqrt1 x (remainder (square x) m)))
(cond ((= exp 0) 1)
      ((even? exp) (squaremod-with-check (miller-rabin-expmod base (/ exp 2) m)))
      (else (remainder (* base (miller-rabin-expmod base (- epx 1) m)) m))))



(define (miller-rabin-test n)
    (define (try-it a)
        (define (check-it x)
	    (and (not (= x 0)) (= x 1)))
	(check-it (miller-rabin-expmod a (- n 1) n)))
(try-it (+ 1 (random (- n 1)))))


