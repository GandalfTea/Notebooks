


# 	Chapter 5 : Designing with Objects




# Are you thinking procedually?

If you are thinking of what a program has to do simply like : " It has to take that and do that and return that ", you are thinking procedually.



# The Object-Oriented Philosophy :

In an object oriented program, you have to ask " What real world objects am I moddeling? " unlike " What does this program do? ". OOP is based on the notion that a program should be devided into models of phisical objects not tasks.




# Classes :

A class helps distinguish an object from it's definition. An orange instance is varied from the general definition of an orange. A class is simply the encapsulation of what defines a clasification of objects.



# Components :

Objects can be broken down into more components.
A component is essentially the same thing as a class, just smaller and more specific.



# Properties :

Properties are what distinguishes objects from one another. Those are the particulars of an instance. An orange might be darker or sweeter.

Properties are class level, all objects have them. Properties parameters are individual.



# Behaviors :

They answer one of two questions :

What does this object do?
What can I do to this object?

In the case of an orange, it does not do much but it can have much done to it.



Behaviors can be class level, all objects share them. Instances can also have different behaiors.

In OOP the bulk of functional code is moved out of procedures and into classes.
Behaviors are implemented in the so called class methods.







#	OBJECT RELATIONSHISPS :



* The HAS-A Relationship :


Also called AGGREGATION, A has a B or A contains a B. One object is part of another. Mostly components.

A zoo has a monkey.



* The IS-A Relationship :

Also called INHERITANCE

A is a B.

A monkey IS A animal. What this means is that a programer can define a Animal class that incapsulates all the properties (size, location, diet, etc) and behaviors (move, eat, sleep) that pertain every animal. The specific animals, such as monkey become derrived classes of Animals.






#	INHERITANCE TECHNIQUES :



* Adding Functionality :

A derived class can augment it's parent class by adding functionality.
A monkey can swing from trees. In adition to the Animal class, it can have swingFromTrees().


* Replacing Functionality :

A derived class can replace or override a method of the parent class. 
Most animals move by walking, so it makes sense to have a method move() that simulates walking. A kangaroo jumps. All the other properties and methods still apply, but the Kangaroo class changes move().


* Adding Properties :

A derived class can also add properties outside of the inherited ones.
A penguin also has a beakSize property.


* Replacing Properties :

You can override properties similar to methods. Doing so is rarely appropiate because it hides the property from the base class.




#	POLYMORPHISM VERSUS CODE REUSE :


Polymorphism 


















