

### OOP

&nbsp;


#### enumerating properties

The traditional way is using the for...in loop:

```js
const SYM = Symbol();

const o = { a: 1, b: 2, c: 3, [SYM]: 4 };

for(let prop in o) {
	if(!o.hasOwnProperty(prop)) continue;
	console.log(`${prop}: ${o[prop]}`);
}
```

`hasOwnProperties` addresses the danger of inherited properties. 

&nbsp;


#### Object.keys

This gives us access to all the enumerable string properties of an object.        
Using the prev. example:

```js
Object.keys(o).forEach( prop => console.log(`${prop}: ${o[prop]}`));
```

&nbsp;


#### Class and Instance

```js
class Car {
	constructor(make, model) {
		this.make = make;
		this.model = model;
		this._userGears = ['P', 'N', 'R', 'D'];
		this._userGear =i this._userGears[0];
	}
	
	get getGear() { return this._userGear; }	

	set userGear(value) {
		if(this._userGears.indexOf(gear) < 0)
			throw new Error(`Invalid gear: ${value}`);
		this._userGear = value;
	}
	
	shift(gear) { this.userGear = gear; }
}
```
```js
const car1 = new Car("Tesla", "Model S");
const car2 = new Car("Mazda", "3i");

car1.shift('D');
```

The `instanceof` operator tells you if a given object is an instance of a given class:

```js
car1 instanceof Car		// True
car1 instanceof Array	// False
```


If you want to enforce privacy to the class (more than the underscore private joke) you can use a WeakMap (more in Chapter 10):

```js
const Car = function() {
	
	const carProps = new WeakMap();

	class Car {
		constructor(make, model) {
			this.make = make;
			this.model = model;
			this._userGears = ['P', 'N', 'R', 'D'];
			carProps.set(this, { userGear: this_userGear[0]} );
		}
			
		get getGear() { return carProps.get(this).userGear; }	

		set userGear(value) {
			if(this._userGears.indexOf(gear) < 0)
				throw new Error(`Invalid gear: ${value}`);
			carProps.get(this).userGear = value;	
		}
			
			shift(gear) { this.userGear = gear; }
	}

	return Car;
})();
```
&nbsp;


#### Class History

Prior to ES6 introducing the `class` kayward, you would have to define a function that would serve as a constructor.

```js
function Car(make, model) {
	this.make = make;
	this.model = model;
	this._userGears = ['P', 'N', 'R', 'D'];
	this._userGear = this._userGeard[0];
}
```
&nbsp;


#### Prototype


When refering to a class method that applies to a specific instance, that method is a _prototype_ method. For example, when refering to the _shift_ method on a _Car_ instance, you refer to _Car.prototype.shift_. This is sometimes also written as _Car#shift_.

Every function has a property called _prototype_. For most, it is not used. It becomes useful when the _new_ keyward is used. The newly created object has access to the constructor's prototype and stores it into it's `__proto__` property. 

What's important about the _prototype_  is the mechanism called _dynamic dispatch_. If an object has a method call and the method doesn't exist, JS checks the object's prototype to check if it exists there. All instances of the same class share the prototype, therefore if there is a method on the prototype, all instances have access to it.o

```js
const car1 = new Car();
const car2 = new Car();

car1.shift === Car.prototype.shift;		// True
car1.shift('D');

car1.shift = function(gear) { this.userGear = gear.toUpperCase(); }
car1.shift === Car.prototype.shift;		// False
```
&nbsp;


#### Static 

Methods belonging to the class, not instances. 

```js
static areSimilar(car1, car2) {
	return car1.make === car2.make && car1.model === ca2.model;
}
```
&nbsp;


#### Inheritance

```js
class Vehicle {
	. . .
}

class Car extends Vehicle {
	. . .
}
```
&nbsp;


#### Polymorphism

Objects belonging not only to the main class, but also inherited classes. 
Nothing special here, no special keyward for function override, overloading, etc.


&nbsp;


#### String Representation

Every object you create is ultimately inherited from the _Object_ superclass. One of the methods inherited is `toString`:

```js
class Clar {
	toString() {
		return `${this.make} ${this.model}: ${this.vin}`;	// vin does not matter, just a property
	}
}
```

&nbsp;


#### Multiple Inheritance, Mixins and Interfaces


Javascript only supports one inheritance because the prototype chain does not look for multiple parents, but it provides ways to bypass this restriction. 

A _mixin_ refers to the functionality that is _mixed in_ as needed. 

As an example, an insurance for the car function in the shape of a _mixin_ can be:


```js
class InsurancePolicy() {}
function makeInsurable(o) {
	o.addInsurancePolicy = function(p) { this.insurancePolicy = p; } 
	o.getInsurancePolicy = function() { return this.insurancePolicy; }
	o.isInsured = function() { return !!this.insurancePolicy; }   
}
```
```js
const car1 = new Car();

car1.addInsurancePolicy(new InsurancePolicy());		// Error

makeInsurable(car1);
car1.addInsurancePolicy(new InsurancePolicy());		// Works
```

One way to bypass the need for this is by doing:

```js
makeInsurable(Car.prototype);
```





