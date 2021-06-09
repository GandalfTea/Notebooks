

### Literals, Variables, Constants and Data Types:


#### Declaring a variable

```js
let variable = 5;
let var = "Hello", var2 = "There";
```
```js
const CPU_CLOCK = 2000
```

Primitives:

* Number
* String
* Bool
* Null
* Undefined
* Symbol

Object types:

* Array
* Date
* RegExp
* Map and WeakMap
* Set and WeakSet
         
         
#### Numbers

JS has only one numeric data type. 

```js
let number = 10;

number = 0x0000ff;		// Hex
number = 0o0022;		// Octal
number = 3.0e6;			// Exp
number = -1.6e-19;		// Exp
number = Inifity;	
number = -Infinity;
number = NaN;			// Not A Number
```

There is also a _Number_ object:

```js
const small = Number.EPSILON; 			// the smallest value that can be
						// added to 1 to get a distinct number
						// larger than 1, approx. 2.2e-16
const bigInt = Number.MAX_SAFE_INTEGER; 	// the largest representable integer
const max = Number.MAX_VALUE; 				// the largest representable number
const minInt = Number.MIN_SAFE_INTEGER; 	// the smallest representable integer
const min = Number.MIN_VALUE; 				// the smallest representable number
const nInf = Number.NEGATIVE_INFINITY; 		// the same as -Infinity
const nan = Number.NaN; 					// the same as NaN
const inf = Number.POSITIVE_INFINITY; 		// the same as Infinity
```

#### Strings

Strings are Unicode.

Escaping:

```js
'Sam looked up, and said "Hello there, Genera Kenobi", as Yoda walked in.'
"Don't do that"
"A backslash \\ escapes itsef"
```

Inserting in string:

```js
let currentTemp = 19.5;
const message = "The current temperature is " + currentTemp + "\u00b0c";
const message = "The current temperature is ${currentTemp}\u00b0c";		// ES6
```

Multiline:

```js
const multiline = "line1 \
				   line2";
```
```js
const multiline = `line 1		// Newline plus white space
			       line 2
				   line 3`;
```

#### null and undefined:

The programmer should use null, while undefined is assigned by the program. This is not enforced, however.

```js
let currentTemp;			// implicit value of undefined
const targetTemp = null;
currentTemp = 19.5;
currentTemp = undefined;	// apeares uninitialized again
```
          
         
#### Objects

```js
const obj = { };
```
```js
obj.color = "yellow";

obj.["not an identifier"] = 3;	// Not a valid identifier, but works.
obj.["not an identifier"];		// 3

const SIZE = Symbol();
obj[SIZE] = 8;
obj[SIZE];						// 8
```


```js
const sam1 = {
	name: 'Sam',
	age: 4,
};

const sam2 = { name: 'Sam', age: 4 };

const same = {
	name: 'Sam',
	classification: {
		kingdom: 'Anamalia',
		phylum: 'Chordata',
		class: 'Mamalia',
		order: 'Carnivoria',
		family: 'Felidae',
		subfaimily: 'Felinae',
		genus: 'Felis',Î
		species: 'catus',
	}
}
```


Can be accessed in various ways: 

```js
sam3.classification.family; // "Felidae"
sam3["classification"].family; // "Felidae"
sam3.classification["family"]; // "Felidae"
sam3["classification"]["family"]; // "Felidae"
```

Objects can also have member functions:

```js
sam3.speak = function() { return "Meow"; };
```

You can also delete proeperties:

```js
delete sam3.clasification;
delete sam3.speak;
```

        
         
#### Arrays

They are not fixed in size, each element can be of any type and are zero based.

```js
const a = [1, "two", 3, null];			// mix

const a = [								// objects
	{ name: "Ruby", hardness: 9 },
	{ name: "Diamond", hardness: 10 },
	{ name: "Topaz", hardness: 8 },
];

const a = [								// 2D arrays
	[1, 2, 3],
	[null, null, null],
	[null, 2, "three"],
];
```

Properties:
```
array.length;
array[0];
```
          
           
#### Dates

```js
const now = new Date();
now; // example: Thu Aug 20 2015 18:31:26 GMT-0700 (Pacific Daylight Time)
```

```js
const halloween = new Date(2016, 9, 31); // note that months are
										 // zero-based: 9=October
```

```js
const halloweenParty = new Date(2016, 9, 31, 19, 0); // 19:00 = 7:00 pm
```

Components:

```js
halloweenParty.getFullYear(); 		// 2016
halloweenParty.getMonth(); 			// 9
halloweenParty.getDate(); 			// 31
halloweenParty.getDay(); 			// 1 (Mon; 0=Sun, 1=Mon,...)
halloweenParty.getHours(); 			// 19
halloweenParty.getMinutes(); 		// 0
halloweenParty.getSeconds(); 		// 0
halloweenParty.getMilliseconds(); 	// 0
```
        
        
#### regex

It is almost a sub-language of JS and it offers a compact way to perform complex search and replace operatios on a string.


```js
// extremely simple email recognizer
const email = /\b[a-z0-9._-]+@[a-z_-]+(?:\.[a-z]+)+\b/;
// US phone number recognizer
const phone = /(:?\+1)?(:?\(\d{3}\)\s?|\d{3}[\s-]?)\d{3}[\s-]?\d{4}/;
```
