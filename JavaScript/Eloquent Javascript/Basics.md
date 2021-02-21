 This file contains the first 3 chapters of the textbook.     
      
 
 * [Values, Types and Operators](#1)
 	*  [Values](#1.1)
 	*  [Logical Operators](#1.2)
 	*  [Automatic conversion](#1.3)
 * [Program Structure](#2)
 	* [Expressions and Statements](#2.1) 
 		* [Binding variables](#2.1.1)
 	* [Control Flow](#2.2)
 	* [Comments](#2.3)
 * [Functions](#3)
 	* [First method](#3.1)
 	* [Second method](3.2)
 	* [Third Method](#3.3)
 	* [Closure](#3.4)
 	* [Recursion](#3.5)
 	
 		

&nbsp;


## Values, Types and Operators  <a name="1"></a>

&nbsp;

### Values  <a name="1.1"></a>

&nbsp;

#### Numbers  <a name="1.1.1"></a>

You can use `e` to represent exponents:
```js
2.998e8
```
is 2,998 x 10^8 = 299800000

There are 3 special numbers:      
`Infinity` which represents positive infinity value.     
`-Infinity` negative infinity value.      
`NaN` Not A Number. Meaning even if it is a value of number type, error. For trying to do 0 / 0 or Infinity - Infinity.     

&nbsp;

#### Strings  <a name="1.1.2"></a>

You can use backticks, single or double quotes.
```js
`String`
'String'
"String"
```
You can use `\n` for newline in string.      
Concatination is like Python.

You can insert info into a string like this:
```js
"half of 100 is ${100 / 2}"
```

&nbsp;

#### Unary   <a name="1.1.3"></a>
You can use `typeof` to get the type of a value.
```js
console.log(typeof 4.5)
```
```
-> number
```

&nbsp;

#### Boolians <a name="1.1.4"></a>


Like Python.

&nbsp;


#### Logical Operators <a name="1.2"></a>

`&&` is AND     
`||` is OR     
`!` is NOT     

Also the simple if statement:
```js
console.log(true ? 1 : 2);
console.log(false ? 1 : 2);
```
```
-> 1
-> 2
```

&nbsp;

#### Automatic conversion <a name="1.3"></a>

```js
console.log(8 * null)
-> 0
console.log("5" - 1)
-> 4
console.log("5" + 1)
-> 51
console.log("five" * 2)
-> Nan
console.log(false == 0)
-> true
```



&nbsp;


## Program Structure <a name="2"></a>

&nbsp;

### Expressions and Statements <a name="2.1"></a>

&nbsp;

#### Binding variables <a name="2.1.1"></a>

It is done like so:
```js
let caught = 5 * 5;
let ten = 10;
let mood = "light";
mood = "dark";

console.log(mood)
console.log(ten*ten);
```
```
dark
-> 100
```

Can also be stacked:

```js
let one = 1, two = 2;
```

Earlier in JavaScript history, the keyward `var` was used instead of `let`. It can still be used. The difference between them will be discussed in the next chapter. `const` can also be used to declare constants.
```js
var name = "Ayda";
const greeting = "Hello ";
console.log(greeting + name);
```
```
-> Hello Ayda
```

&nbsp;

#### Control Flow <a name="2.2"></a>

&nbsp;


__Input__ can be taken like this: 
```js
let theNumber = Number(prompt("Pick a number"));
```
  
  &nbsp;

__If statements__ look like in C++:
```js
let theNumber = Number(prompt("Pick a number"));
if (!Number.isNaN(theNumber)) {
	console.log(theNumber);
} else {
	console.log("Not a number");
}
```
This only accepts a number.

If you have more conditions:
```js
let num = Number(prompt("Pick a number"));
if (num < 10) {
	console.log("Small");
} else if (num < 100) {
	console.log("Medium");
} else {
	console.log("Large");
}
```

&nbsp;

__while__ loops:
```js
let number = 0;
while (number <= 12) {
	console.log(number);
	number += 2;
}
```

&nbsp;

__do-while__ loops:
```js
let yourName;
do {
	yourName = prompt("What is your name?");
} while(!yourName);
console.log(yourName);
```

&nbsp;

__for__ loops:
```js
for (let number = 0; number <= 12; number += 2) {
	console.log(number);
}
```


To brake out of a loop, use the keyward `break`.

&nbsp;

__switch__ loops:
```js
switch(prompt("What is the weather like?")) {
  case "rainy":
	console.log("Good.");
	break;
  case "sunny":
	console.log("OK");
  	break;
  case "cloudy":
	console.log("OK");
  	break;
  default:
	console.log("Unknown");
	break;
}
````
&nbsp;

#### Comments <a name="2.3"></a>

Single comment
```js
// single comment
```
Multiline comment
```js
/*
this 
is 
a 
multiline
comment
*/
```

&nbsp;


## Functions <a name="3"></a>


To define a function: <a name="3.1"></a>

```js
const square = function(x) {
	return x * x;
};
```
```js
console.log(square(12));
```
```
-> 144
```

It starts with the keyward `function`. 

It can have 0 or more parameters.

&nbsp;


Scopes are the same as C++.     
`var` variables are visible throuought the function.      
`let` variables are only local to the code block.     

```js
let x = 10;
if (true) {
	let y = 20;
	var z = 30;
	console.log(x + y + z);
}
//y is not visible here
convole.log(x + z);
```

&nbsp;


Functions can be passed as arguments and redefied:
```js
let launchMissiles = function() {
	missileSystem.launch("now");
};

if (safeMode) {
	launchMissiles = function() {/* do nothing */};
}
```

&nbsp;

There is another way to create a function, which worked differently: <a name="3.2"></a>
```js
function square(x) {
	return x * x;
}
```

This is a _function declaration_. The difference is that, at runtime, they are processed beforehand. It does not matter where in the document the function _declaration_ is located, it will run:
```js
console.log("The future says: ", future());

function future() {
	return "You'll never have flying cars.";
}
```

&nbsp;


There is even another way to create a function, with a wierder syntax. <a name="3.3"></a>
```js
const power = (base, exponent) => {
	let power = 1;
	for (let count = 0; count < exponent; coint++) {
		result *= base;
	}
	return result;
};
```
It uses the `=>` symbol.  It can also omit some syntax details if there is only one parameter or one line to be executed:
```js
const square1 = (x) => { return x * x };
const square2 = x => x * x;
```

If it has no parameters, you use simple `()`:
```js
const horn = () => {
	console.log("Toot");
};
```

There is no reason for this syntax. It does mostly the same thing as any function expression.

&nbsp;


You can have optional arguments.
```js
function square(x) { return x * x; }
console.log(square(4, true, "hedgehog"));
```
```
-> 16
```

If you pass too many arguments, the extra ones are ignored. If you pass too few, the missing ones are assigned the value `undefined`. 

```js
function minus(a, b) {
	if (b == undefined) return -a;
	else return a - b;
}
```
```js
console.log(minue(10));
console.log(minus(10,5));
```
```
-> 10
-> 5
```

&nbsp;


You can have predefined values for parameters:
```js
function power(base, exponent = 2) {
	...
}
```

&nbsp;

#### Closure <a name="3.4"></a>

What happens to local bindings when the function call is no longer available?
```js
function wrapValue(n) {
	let local = n;
	return () => local;
}

let wrap1 = wrapValue(1);
let wrap2 = wrapValue(2);

console.log(wrap1());
console.log(wrap2());
```
```
-> 1
-> 2
```
The feature to reference a specific instance of a local binding in an enclosed scope is called _closure_. A function that references bindings from local scopes is called _a_ closure. Another example:
```js
function multiplier(factor) {
	return number => number * factor;
}

let twice = multiplier(2);
console.log(twice(5));
```
```
-> 10
```


&nbsp;

#### Recursion <a name="3.5"></a>

You can do recursion

```js
function power(base, exponent) {
	if (exponent == 0) {
		return 1;
	} else {
		return base * power(base,exponent - 1);
	}
}
```
```js
console.log(power(2,3));
```
```
-> 8
```

Another example. Starting from number 1, either add 5 or multiply 3 to reach a given number:
```js
function findSolution(targer) {
	function find(current, history) {
		if(current = target) {
			return history;
		} else if (current > target) {
			return null;
		} else {
			return find(current + 5, "(${history} + 5)") ||
			       find(current * 3, "(${history} * 3)");
		}
	}
	return find(1, "1");
}
```
```js
console.log(findSolution(24));
```
```
-> (((1 * 3) + 5) * 3)
```

In this case the OR (`||`) chooses between the first and second option. If the first returns something that is not `null`, it is returned. Otherwise it returns the second one, even if `null`.







