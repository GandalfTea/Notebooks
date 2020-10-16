

# 2.2 Hierarchical Data and the Closure Property :



The ability to create pairs whose elements are pairs is the essance of list structure's importance as a representation tool. We refer to this ability as the closure property of cons. An operation satisfies the closure property if the operation can also combine the objects created with the operation.

Closure permits the creation of hierarchical structures.





# 2.2.1	Representing Sequences :


One example of a sequence  :

> (cons 1 
	(cons 2 
		(cons 3 
			(cons 4 nil))))

This is a chain of pairs. The car is the item and cdr is the next item. The cdr of the last pair points to a value not in the pair and signals the end of the sequence, usually the variable > nil.

This data structure is called a list, and scheme presents a primitive to form one :

> (list 1 2 3 4)
> (list a1 a2 a3 ... an)

it is equivalent to

> (cons a1
	(cons a2
		(cons ...
			(cons an nil))...))



This structure is evaluated by lisp in the form :

(1 2 3 4)
(a1 a2 a3 ...an)



> (define one-through-for (list 1 2 3 4))

> (car one-thhrough-for)
1

> (cdr one-through-for)
(2 3 4)

> (car (cdr one-through-for))
2

> (cons 10 one-through-for)
(10 1 2 3 4)



NOTE : The term list structure refers to any data structure made out of pairs, not just lists.



# List operations


Procedures :


* list-ref : takes a list and a number and returns the object at that position in the list.

Works like :
> (define (list-ref items n)
	(if (= n 0)
 	    (car items)
	    (list-ref (cdr items) (- n 1))))

> (define squares (list 1 4 9 16 25))

> (list-ref squares 3)
16




* length : returns the number of items in the list.

> (define (length items)
	(if (null? items)
	     0
	    (+ 1 (length (cdr items)))))

> (define odds (list  1 3 5 7))

> (length odds)
4




* append : combines two lists.

> (define (append list1 list2)
	(if (null? list1)
	     list 2
	    (cons (car list 1) (append (cdr list 1) list 2))))

> (append squares odds)
( 1 4 9 16 25 1 3 5 7)






# Mapping over Lists :


Modifying each element :

> (define (scale-list items factor)
	(if (null? items)
	     nil
	    (cons (* (car items) factor)
		     (scale-list (cdr items)
				 factor))))

> (scale-list (list 1 2 3 4 5) 10)
(10 20 30 40 50)

From this we can create a higher order procedure called map. Map takes a list and a procedure of one argument and returns a list of the results produced by applying the procedure to all items in the list.

> (define (map proc items)
	(if (null? items) 
	     nil
	    (cons (proc (car items))
		  (map proc (cdr items)))))


Scheme already has a built in map procedure that is more generalized.

> (map abs (list -10 2,5 -11 17))
(10 2,5 11 17)

> (map (lambda (x) ( * x x)) (list 1 2 3 4))
(1 4 9 16)


Now we can rewrite the definition of scale-list using map :

> (define (scalel-list items factor)
	(map (lambda (x) (* x factor))
	      items))



# 2.2.2 Hierarchical Structures


Lists can also contain other lists :

> (cons (list 1 2) (list 3 4))
((1 2) 3 4)

Another way o think of this is as trees. :

	((1 2) 3 4)
	  /  |  \
      (1 2)  3   4
       / \
      1   2



Recursion is a natural tool for dealing with tree structures because we can deal with the trunk, than branches than branckes of branches than leaves.

Example :

> (define x (cons (list 1 2) (list 3 4)))
  (length x)
: 3

> (count-leaves x)
: 4


To build count-leaves, we must not only add one to the cdr, but to add count-leaves of the car to cout-leaves of the cdr. This is because car might be a list.

To aid in this, Scheme provides a primitive named pair? witch tests whether it's argument is a pair. Used like :


> (define (count-leaves x)
     (cond ((null? x) 0)
	   ((not (pair? x)) 1)
	   (else (+ (count-leaves (car x))
		    (count-leaves (cdr x)))))) 


# Mapping over Trees

Map together with recursion is a powerful abstraction for dealing with trees :

> (define (scale-tree tree factor)
     (cond ((null? tree) nil)
	   ((not (pair? tree)) (* tree factor))
	   (else (cons (scale-tree (car tree) factor)
		       (scale-tree (cdr tree) factor)))))

> (scale-tree (list 1 (list 2 ( list 3 4) 5) (list 6 7)) 10)
: (10 (20 (30 40) 50) (60 70))


Another way to implement scale-tree is to section the tree into sequences of sub-trees and use map :


> (define (map proc items)
	(if (null? items) 
	     nil
	    (cons (proc (car items))
		  (map proc (cdr items)))))

> (define (scale-tree tree factor)
      (map (lambda (sub-tree)
		(if (pair? sub-tree)
		   (scale-tree sub-tree factor)
		   (* sub-tree factor)))
            tree))

Many operations can be implemented by similar combinations of sequences and recursion.



# Sequences as Conventional Interfaces :

Let's take two procedures as examples, one of them adds the odd squares of a tree and the other computes the even fibonacci numbers Fib(k) where k <= n.


> (define (sum-odd-squares tree)
      (cond ((null? tree) 0) 
	    ((not (pair? tree)) (if (odd? tree) (square tree) 0))
            (else (+ (sum-odd-squares (car tree))
		     (sum-odd-squares (cdr tree))))))

> (define (even-fibs n)
      (define (next k)
	  (if (> k n)
	       nil
	      (let ((f (fib k)))
		  (if (even? f)
		      (cons f (next (+ k 1)))
		      (next (+ k 1)))))))


On the surface, these procedures are very different, but the general ideea is the same: Enumerate something, pass it through a filter, map it and than accumulate into the end result.


A signal processing engineer would find it natural to think of those procedures as the signal flowing through some stages, each of witch implement a part of the program.

If we could rewrite those procedures to manifest the signal-flow structre, that would make them easier to understand.


# Sequance operations :

If we represent the signal as a list, than we can implement each stage separately, like we use the procedure map to map it.

Filtering a sequence to only select certain elements is done like this :

> (define (filter predicate sequence)
     (cond ((null? sequence) nil)
	   ((predicate (car sequence))
		(cons (car sequence)
			(filter predicate (cdr sequence))))
	   (else (filter predicate (cdr sequence)))))


> (filter odd? (list 1 2 3 4 5))
:(1 3 5)


Accumulations can be implemented by :


> (define (accumulated op initial sequence)
     (if (nul? sequence)
	  initial
	 (op (car sequence)
	     (accumulate op initial (cdr sequence)))))

> (accumulate + 0 (list 1 2 3 4 5))
: 15

> (accumulate * 1 (list 1 2 3 4 5))
: 120

> (accumulate cons nil (list 1 2 3 4 5))
: (1 2 3 4 5)


All that remains to implement is a function to enumerate the sequence of emelements to be proccesed, for even-fibs we need a range :

> (define (enumerate-interval low high)
     (if (> low high)
	  nil
	 (cons low (enumerate-interbal (+ low 1) high))))

> (enumerate-interval 2 7)
: (2 3 4 5 6 7)



To enumerate the leaves of a tree we use :


> (define (enumerate-tree tree)
     (cond ((null? tree) nil)
	   ((not (pair? tree)) (list tree))
	   (else (append (enumerate-tree (car tree))
			 (enumerate-tree (cdr tree))))))

> (enumerate-tree (list 1 (list 2 (list 3 4)) 5))
: (1 2 3 4 5)



Now we can reformulate sum-odd-squares and even-fibs as in the signal flow chart :

> (define (sum-off-squares)
     (accumulate + 0 (map square (filter odd? (enumetare-tree tree)))))



> (define (even-fibs n)
     (accumulate
	cons
	nil
	(filter even? (map fib (enumerate-interval 0 n)))))

This type of program structure helps us in creating modular procedures. We can encurage modular design by providing a library of standard components together with a conventional interface for connectign  the components in flexible ways.


Modular construction is a powerful tool to control complexity in design. 


Suppose we want to check witch programer has the biggest salary. We can do this like so :

> (define (salary-of-highest-paid-programmer records)
	(accumulate max 0 (map salary (filter programmer? records))))

This is the power of modular code.


NOTE: I feel like such a dumbass man, this is so fucking smart.




# Nested Mappings

We can extend the sequence paradigm to include the computations most commonly expressed as nested loops :

Given an int n, fill all ordered pairs of distinct positive int i and j where, 1 <= j < i <= n, such that i + j is prime :

A natural way to solve this is to compute all posible pairs, filter out those that filter to prime and return the triple (i, j, i+ j). For each int i <= n, enumerate j < i and for each i and j, make a pair. We map along the sequence (enumerate-interval 1 n) for i and (enumerate-interval 1 (- n 1)). For each j we make a pair (i, j) :


> (accumulate
       append
       nil
       (map (lambda (i) (map (lambda (j) 
			         (list i j))
	   	             (enumerate-inteerval 1 (- i 1))))
       (enumerate-interval 1 n)))

This procedure is so common that we should define it as :


> (define (flatmap proc seq)
     (accumulate append nil (map proc seq)))

Now filter the prime pairs :



# NOTE: (cadr pair) is the car of the cdr!

> (define (prime-sum? pair)
      (prime? (+ (car pair) (cadr pair)))) 


Make the final pair :

> (define (make-pair-sum pair)
      (list (car pair) (cadr pair) (+ (car pair) (cadr pair))))




Now combine everything into the big procedure :

> (define (prime-sum-pairs n)
      (map make-pair-sum
	     (filter prime-sum? (flatmap
				    (lambda (i)
					(map (lambda (j) (list i j))
					     (enumerate-interval 1 (- i 1))))
				    (enumerate-interval 1 n)))))



Nested mappings are also useful for sequences other than enumerate intervals.

Suppose we wanted to create all the permutations of a list :

Ex for list (1 2 3) : (1 2 3) (1 3 2) (2 1 3) (2 3 1) (3 2 1) (3 1 2)


Take a int x from a list S. Recursively generate all the permutations of S - x and than add x to the front. Than combine the lists for all the x's in S.

> (define (permutations s)
     (if (null? s)		: empty set?
	 (list nil)		: sequence containing empty set
	 (flatmap (lambda (x)
			(map (lambda (p) (cons x p))
			     (permutations (remove x s))))
		  (s))))

> (define (remove item sequence)
     (filter (lambda (x) (not (= x item)))
	     sequence))

