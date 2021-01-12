

##	Chapter 3 : Designing Professional C++ Programs



&nbsp;

### What is Programming Design?


#### 1. Analyze the requirements :

A vital outcome of this analysis is a functional requirements document describing what exactly the new piece of code has to do, but not yet how to do it. Together with this, there should be a non-functional requirements document describing how the final system should be, not what it should do. Examples of non-funcional requirements are : security, extensiblility, satisfy certain performance criteria, etc.



#### 2.  Software Design :

The program or software design is the architecture that will implement the requirements, functional and non-functional. This should be done in the form of a design document, witch includes 2 parts :
	
* The gross subdivision of the program into subsystems, including interfaces and dependencyes between the subsystems, data flow between them, input and output to and fro all of them and a general threading model.

* The details of each subsystem, including subdivision into classes, class hierarchies, data structures, algorithms, a specific threading model and error-handeling specifics.

The design documents should include diagrams and tables showing subsystem interactions and class hierarchies. The Unified Modeling Language (UML) is the general standard for such diagrams.


&nbsp;


### Designing for C++ :

* C++ has an immense feature set.
* C++ is object-oriented.
* C++ has numerous ways to design generic or reusable code. You can use templates or opreator overloading.
* C++ has a useful Standard Libraty.
* C++ acomodates many design patterns.


&nbsp;


### Two rules for C++ design :

#### 1. Abstraction

#### 2. Reuse


	
	One way to implement reuse is by templait. A basic chess-board template example is :
```
	  template <typename PieceType>
	  class GameBoatd
	  {
		public :
			void setPieceAt (size_t x, size_t y, PieceType* piece);
			PieceType* getPieceAt(size_t x, size_t y);
			bool isEmpty(size_t x, size_t y) const;
		private:
	  };
```
	
&nbsp;


### Also to keep in mind :

* Understand the performance.
* Understand platform limitations.
* Understand Licensing and Support.
* Prototype.






