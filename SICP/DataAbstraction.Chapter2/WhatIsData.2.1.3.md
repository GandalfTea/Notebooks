




# 2.1.3 What is meant by DATA?

In general, we can think of data as a set of constructors and selectors, together with specified conditions that those procedures must fulfill in order to be a valid representation.


//no ideea what this is or what it does
// has no output

> (add-1 zero) = (lambda (f) (lambda (x) (f (((lambda (f) (lambda(x) x)) f) x))))
		 (lambda (f) (lambda (x) (f (f x))))
		 one is  (lambda (f) (lambda (x) (f x)))

> (add-1 one)   = (lambda (f) (lambda (x) (f (((lambda (f) (lambda (x) (f x))) f) x))))
		= (lambda (f) (lambda (x) (f ((lambda (x) (f x)) x))))  ????
		= ?
