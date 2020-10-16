

# 2.17 :

Define a procedure that returns the last item of a list :

(define (last-pair a)
	(list-ref a (- (length a) 1))


# 2.18

A procedure that reverses a list :

> (define (reverse a)
	(cons (last-pair a) (reverse (length a 
	



# 2.19


Using lists in procedures :

> (define us-coins (list 50 25 10 5 1))
> (define uk-coins (list 100 50 20 10 5 2 1 0.5))


> (define ((cc amount coin-values)
	(cons ((= amount 0) 1)
	      ((or (< amount 0) (no-more? coin-values)) 0)
	      (else
		(+ (cc amount (except-first-denomination coin-values))
		   (cc (- amount (first-denomination coin-values)) coin-values))))))


Define :

except-first-denomination
first-denomination
no-more?

------------------------------------------------------------------------------------


> (define (except-first-denomination a)
	(cdr a)

> (define (first-denomination a)
	(car a)

> (define (no-more? a)
	(null? a))






# 2.21


> (define (square-list items)
	(define (iter things answer)
	    (if (null? things)
		 answer
		(iter (cdr things)
		      (cons (square (car things))
			     answer))))
  (iter items nil))

> (square-list (list 1 2 3 4)
(16 9 4 1)

> (define square-list items)
	if (null? items)
	    nil
	   (cons (square (car items)) (square-list (cdr items))


# 2.24

	(list 1 (list 2 (list 3 4)))
	   /            |
	  1	    (2 (3 4)
		    /    |
		   2   (3 4)
		       /   \
		      3     4
(1 (2 (3 4)))



# 2.25

pick seven from list using car and cdr :

(1 3 (5 7) 9)
(cdr (cdr (cdr (car))))

((7))
(car (car))

(1 (2 (3 (4 (5 (6 7))))))
(cdr (cdr (cdr (cdr (cdr (cdr))))))


# 2.26

(append x y)
(1 2 3 4 5 6)

(cons x y)
(x y)

(list x y)
(x y)


# 2.27

Create a deep reverse procedure :

> (define x (list (list 1 2) (list 3 4)))
((1 2) (3 4))

> (reverse x)
((3 4) (1 2))

> (deep-reverse x)
((4 3) (2 1))

> (define (deep-reverse items)
	(define (iter things answer)
	    (if (null? things)
		 answer
		(iter (cdr things)
		      (if (pair? (car things))
			  (iter (car items) answer)
			  (cons (car things) answer)))))
  (iter items nil))


> (deep-reverse x)
: (4 3 2 1)
//no fucking ideea how to fix this.



# 2.28

write a procedure (fringe tree) and returns everything arranged :

> (fringe x)
:(1 2 3 4)

jesus fuckign christ this is dumb.

> (define (deep-reverse items)
	(define (iter things answer)
	    (if (null? things)
		 answer
		(iter (cdr things)
		      (if (pair? (car things))
			  (iter (car items) answer)
			  (cons (car things) answer)))))
 (revers (iter items nil)))


# 2.31

Abstract a property that iterates through trees and applies procedures.


This is wrong :
> (define (tree-map proc tree)
     (if (null? tree)
	  nil
	  (if (pair? (car tree))
	      (tree-map proc (car tree)
	      (cons (proc (car tree))

This works :
> (define (tree-map proc tree)
    (cond ((null? tree) nil)
	  ((not (pair? tree)) (proc tree))
	  (else (cons (tree-map proc (car tree))
		      (tree-map proc (cdr tree))))))




# 2.34

start with an, * x, + an-1, * x and so on until a0.

> (define (accumulate op initial sequence)
      (if (null? sequence)
 	   nil
	  (op (car sequence)
	      (accumulate op initial (cdr sequence))))) 

Assume he coeficient is ordered a0...an.

> (define (horner-eval x coefficient-square)
     (accumulate (lambda (this-coeff higher-terms) (+ this-coeff (* higher-terms x)))
		  0
		 coefficient-sequence))

  For 1 + 3x +5x^3 +x^5 at x = 2 :
> (horner-eval 2 (list 1 3 0 5 0 1))

Cannot try because accumulate does not run. Probs good for all I know



# 2.37

Supposse we represent vectors as sequences of numbers and matrices as sequences f vectors (rows) :
 
 (1 2 3 4
  4 5 6 6
  6 7 8 9)

is : ((1 2 3 4) (4 5 6 6) (6 7 8 9))






# 2.42


The Eight Queen problem :


How to place eight queens on the chessboard so that none are in check?

Possible solution :
Place a queen on each row and iterate through all the possible positions of that row. Filter the ones where the queen is not in check. This produces all the possible variants.

> (define (flatmap proc seq)
     (accumulate append nil (map proc seq)))


> (define (queen board-size)
     (define (queen-cols k)
         (if (= k 0)
	     (list empty-board)
	     (filter (lambda (positions) (safe? k positions))
		(flatmap (lambda (rest-of-queens)
		         (map (lambda (new-row)
				      (adjoin-position new-row k rest-of-queens)
			      (enumerate-interval 1 board-size)))
		         (queen-cols (- k 1)))))))
     (queen-cols board-size))

> (define empty-board nil)

> (define (adjoin-position row column board)
     (cons (cons row column) board))

> (define (get-row position)
     (car position))

> (define (get-column position)
     (cdr position))

> (define (get-first-in-column column position)
     (car (filter (lambda (position)
			(= (get-column position) column))
		    positions)))

> (define (filter-out-positions filter-position positions)
     (let ((row (get-row filter-positions))
	   (column (get-column filter-positions)))
	(filter (lambda (position)
		  (not (and (= (get-row position) row)
			    (= (get-column position column)))))
	 	positions)))


> (define (is-safe-position? row column other-row other-column)
     (let ((other-column-diff (- column other-column)))
	   (and (not (= row other-row))
		(not (= column other-column))
		(not (= (+ roe other-column-diff) other-row))
		(not (= (- roe other-column-diff) other-row)))))


> (define (safe? column position)
     (let ((test-position (get-first-in-column column positions)))
	(let ((row (get-row test-position))
	      (column (get-column test-position))
	      (other-positions (filter-out-positions test-position positions)))
	   (accumulate (lambda (position preceding-positions-safe)
			  (and (is-safe-position? row
						  column
						  (get-row position)
						  (get-column position))
			       proceding-positions-safe))
			  #t
			  other-positions))))

: (queen 5)
(((4 . 5) (2 . 4) (5 . 3) (3 . 2) (1 . 1))
 ((3 . 5) (5 . 4) (2 . 3) (4 . 2) (1 . 1))
 ((5 . 5) (3 . 4) (1 . 3) (4 . 2) (2 . 1))
 ((4 . 5) (1 . 4) (3 . 3) (5 . 2) (2 . 1))
 ((5 . 5) (2 . 4) (4 . 3) (1 . 2) (3 . 1))
 ((1 . 5) (4 . 4) (2 . 3) (5 . 2) (3 . 1))
 ((2 . 5) (5 . 4) (3 . 3) (1 . 2) (4 . 1))
 ((1 . 5) (3 . 4) (5 . 3) (2 . 2) (4 . 1))
 ((3 . 5) (1 . 4) (4 . 3) (2 . 2) (5 . 1))
 ((2 . 5) (4 . 4) (1 . 3) (3 . 2) (5 . 1)))

NOTE : do not copy the fucking table next time.
 



