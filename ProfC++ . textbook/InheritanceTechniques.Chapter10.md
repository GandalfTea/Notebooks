

## Discovering Inheritance Techniques
#### Chapter 10



### Building Classes with Inheritance


In earlier chapters we talked about the _is-a_ relationship between classes. It is relevant when you want to build a class that builds on another class. One way to accomplish this is to copy the code from one class to another. This is very annoying. 

#### Extending Classes

When defining a class, you can tell the compiler that it is _inheriting_ from another class, witch is called _the parent class_ or _base class_. Extending an existing class gives the new class (which is now a _derived clas_ or _sub-class_) the ability to describe only the way in witch it is different from the parent class. 

```
class Base
{
	public:
		void someMethod();
	protected:
		int mProtectedInt;
	private:
		int mPrivateInt;
};
```

Deriving a class:

```
class Derived : public Base
{
	public:
		void someOtherMethod();
};
```

`Derived` is a full class that inherits everything from the `Base` class.


#### A Client's View on Inheritance


To a client or any other part of your code, an objct of type `Derived` is also an object of type `Base` because of the inheritance. This means that the `public` methods of both are available for use :

```
Derived myDerived;
myDerived.someMethod();
myDerived.someOtherMethod();
```

This is only in one direction, a `Base` object will not have any knowledge or access to `Derived` :

```
Base myBase;
myBase.someOtherMethod();	// Error : Base does not have a someOtherMethod().
```

A pointer or reference towards a `Base` object can also point to a `Derived` object. This is explained later. It is important to know that this works :

```
Base* base = new Derived();
```

However, you can't call any `Derived` methods :

```
base->someOtherMethod();	// Error.
```

#### A Derived Class's View of Inheritance

It is very similar to a normal class. You can use `private` and `protected` data members and methods like it were it's own. In this case, the `Deriverd` class augments the `Base` Class by adding a new method named `someOtherMethod()`. You can define this method ny using data members from `Base`, :

```
void Derived::someOtherMethods() {
	cout << "I can access base class data members" << endl;
	cout << "Its value is " << mProtectedInt << endl;
}
```


In fact, the difference between `private` and `protected` in a class is that `protected` members are available to derrived classes while `pivate` are not :

```
void Derived::someOtherMethos() {
	cout << mPrivateInt << endl;	// Error
}
```

#### Preventing Inheritance

C++ allows you to mark a class as `final`. This means no class can inherit from it. 
```
class Base final
{
	// . . .
};
```
This is no longer possible :
```
class Derived : public Base
{
	// . . .
};
```

### Overriding Methods

Till now, `Derived` only added upon what `Base` already had. You are also able to modify the behaviour of methods that are inherited. 

The catch is that only methods marked with the keyward `virtual` in the base class can pe properlt overridden :

```
class Base
{
	public:
		virtual void someMethod();
	// . . .
};
```

While the `virtual` keyward does have some subtleties, a good rule of thumb is to go ahead and make all the methods `virtual`. The only drawback is a very tiny performance hit. The same holds true for the `Derived` class, all methods should be marked `virtual` :

```
class Derived : public Base
{
	public:
		virtual void someOtherMethod();
}:
```

#### Note : Constructors should not be made virtual, but destructors are ok. Compiler generated destructors are not `virtual`.


#### Syntax for Overriding a Method

You redeclare it exactly as in the base class and add the `override` keyward. Then you go ahead and provide the implementation.

For example, let's override the `someMethod()` method. The original definition is in the `Base.cpp` file :

```
voic Base::someMethod() {
	cout << "This is the original" << endl;
}
```

Note that you do not need to repeat the `virtual` keyward.

Firstly you must declare it in the class definition :

```
class Derived : public Base
{
	public:
		virtual void someMethod() override;
		virtual void someOtherMethod();
};
```


The implementation is specified in the `Derived.cpp` file :

```
void Derived::someMethod() {
	cout << "This is the overriden method." << endl;
}
```

Once a method or destructor is marked as `virtual`, it is virtual for all derived classes even if the keyward is not present. 


### A Client's View of Overridden Methods


All the code works just like before, but now the same function called on different objects does different things :

```
Base myBase;
Derived myDerived;

myBase.someMethod();
myDerrived.someMethod();
```
Output:
```
This is the original.
This is the overriden method.
```

In the case of pointers and references, if you have a `Base` refference that refers to an object that is really `Derived`, calling `someMethod()` will return the one from the `Derived` class:

```
Derived myDerived;
Base& ref = myDerived;

ref.someMethod();	// Calls the Derived version.
```

Still, you cannot access methods and members that are not also present in the `Base` class :

```
Derived myDerived;
Base& ref = myDerived;

myDerived.someOtherMethod();	// Works
ref.someOtherMethod();		// Error
```

You can also cast or assign a `Derived` object to a `Base` because a `Derived` is a `Base`. However, the object loses any knowledge of the `Derived` class at that point :

```
Derived myDerived;

Base assignmentObject = myDerived;	// Assign a Derived to a Base
assignmentObject.someMethod();		// Calls the Base's version
```

### The _override_ Keyward

It is easy to accidently create a new method rather then overriding. 

The following example overrides without the override keyward :

```
class Base
{
	public:
		virtual void someMethod(double d);
};
```
```
class Derived : public Base
{
	public:
		virtual void someMethod(double d);
};
```

This is a correct overriding of the function.
Now, suppose you accidentally use an `int` parameter instead of a `double` :

```
class Derived : public Base
{
	public:
		void virtual someMethod(int i);
};
```
This code does not override `someMethod()` but instead creates a new virtual method. If you try to call it throw a reference, the method of the `Base` class is called :

```
Derived myDerived;

Base& ref = myDerived;
ref.someMehod(1.1);	// Base version;
```

This type of problem can occur when you start updating the base class but forget to update all inherited classe.

You can prevent this using the `override` keyward. :

```
class Derived : public Base
{
	public:
		virtual void someMethod(int i) override;
};
```

This generates a compiler error, to attract your attention that the function is not the same as in the base class.

#### Note : Always use the `override` keyward as a way to secure against this sort of problems. 


### The _virtual_ Keyward

If a method is not `virtual`, you can still override it, but it will be wrong in subtle ways. 


#### Hiding instead of Overriding

Overriding without `virtual` :

```
class Base
{
	public:
		void go() {cout << "go() in Base" << endl; }
};
```
```
class Derived : public Base
{
	public:
		void go() { cout << "go() in Derived" << endl; }
};
```

Attempting to call `go()` on `Derived` appears to work initially:

```
Derived myDerived;
myDerived.go();
```

Output :
```
go() on Derived
```

However, because the method is not `virtual` it is not actually overridden. The `Derived` class created a new method, also called `go()`, that is competely unrelated to the `Base`'s `go()`. To prove this, call the method in the contex of a `Base` pointer :

```
Derived myDerived;
Base& ref = myDerived;
ref.go();
```
Output:
```
go() called in Base
```

This is because the `virtual` keyward was omitted. 

#### Note : Attempting to override a non-`virtual` function will hide the base class definition and will create a new method.


### How `virtual` is Implemented

When a class is compiled in C++, a binary object is created that contains all methods of the class. 

In the case of non-`virtual` methods, the code to transfer contol is hard-coded directly where the method is called based on the compile-time type. This is called ___static binding___, also known as _early binding_.

If the method is `virtual`, the corect implementation is called through the use of a special area of memoty called the ___vtable___ or _virtual table_. Each class that has one or more `virtual` methods has a vtable, and each object of such a class contains a pointer to that table. This _vtable_ contains pointers to the implementations of the `virtual` methods. When a method call is made, the pointer is followed into the vtable and to the appropiate version of the method is called based on the type of the object at run time. This is called ___dynamic binding___ , also known as _late binding_.

As an example :

```
class Base
{
	public:
		virtual void func1() {}
		virtual void func2() {}
		void nonVirtualFunc() {}
};
```
```
class Derived : public Base
{
	public:
		virtual void func2() override {}
		void nonVirtualFunc() {}
};
```

Instances :
```
Base myBase;
Derived myDerived;
```

The `myBase` object contains a pointer to it's _vtable_. The vtable has 2 entries: `func1()` and `func2()`. Those entries point to the implementations of `Base::func1()` and `Base::func2()`.


`myDerived` also contains a pointer to it's vtable, witch also has 2 entries: `func1()` and `func2()`. `func1()` points to `Base::func1()` implementation because it is not overridden. `func2()` entry points to `Derived::func2()`.


```
myBase	
_________ 	         vtable   

vtable pointer ------->   func1 --------> Base::func1() Implementation
	   	          func2 -----/ -> Base::func2() Implementation
				    /
				   /
myDerived			  /
_________		vtable	 /
				/
vtable pointer -------> func1()/
			func2() --------> Derived::func2() Implementation
```

#### Note : None of the vtables contain the `nonVirtualFunc()` because it is not `virtual`.



