
## Data Structures: Objects and Arrays


#### Arrays

Array syntax:
```js
let listOfNumbers = [2, 3, 5, 7, 11];
```
```js
console.log(listOfNumbers[2]);
console.log(listOfNumbers[2 - 2]);
```
```
-> 5
-> 2
```

The methods of arrays are:
```js
console.log(listOfNumbers.length);

listOfNumbers.push(13);
console.log(listOfNumbers);

listOfNumbers.pop();
console.log(listOfNumbers);
```
```
-> 5
-> [2, 3, 5, 7, 11, 13]
-> [2, 3, 5, 7, 11]
```

#### Objects


Syntax for objects:
```js
let day1 = {
	squirrel: false,
	events : ["work", "touched tree", "pizza", "running"]
};
```
```js
console.log(day1.squirrel);
console.log(day1.wolf);

day1.wolf = false;
console.log(day1.wolf);
```
```
-> false
-> undefined
_> false
```
Inside the object there is a list of properties separated by a comma. The names can also be strings:
```js
let descriptions = {
	work : "Went to work",
	"touched tree" : "Touched a tree"
};
```

Methods for objects:
```js
let anObject = {left : 1, right : 2};
console.log(anObject.left);

delete anObject.left;
console.log(anObject.left);

console.log("left" in anObject);
console.log("right" in anObject);
```
```
-> 1
-> undefined
-> false
-> true
```

The `in` operator tells you whether an object has a propetry with that name. To find out all the propetries, you can use `keys`:
```js
console.log(anObject.keys);
// before we deleted left
```
```
-> ["left", "right"]
```

The keyward `assign` copies all the properties of an object into another:
```js
let objectA = {a : 1, b : 2};
Object.assign(objectA, {b : 3, c : 4});
console.log(objectA);
```
```
-> {a : 1, b : 3, c : 4}
```


As an example, here is an array of objects:
```js
let journal = [
	{events : ["work", "touched tree", "pizza", "running", "television"],
	 squirrel : false},
	{events : ["work", "ice cream", "lasagna"],
	 squirrel : false},
	{events : ["weekend", "cycling", "beer"],
	 squirrel : true},
	. . .
];
```

All the earlier values are _immutable_, like numbers, strings and booleans. If you have a string holding "cat", you cannot change it to "rat" by modifying one letter. You have to change the value of the variable as a whole.
Objects are _mutable_. Properties can be changed.


```js
let object1 = {value : 10};
let object2 = object1;

object1.value += 5;
console.log(object2.value);
```
```
-> 15
```
 You can change this by using the `const` keyward.


#### Log Example

Creating a log that computes the corelation between different activities.

Setting up environment:

```js
let journal = [];

function addEntry(events, squirrel) {
	journal.push({events, squirrel});
}
```

Notice that the object created has the arguments as parameters, not inside parameters. If a property in the braces is not followed by a value, it's value is taken from the name of the parameter.

Adding records:
```js
addEntry(["work", "touched tree", "pizza", "running"], false);
addEntry(["work", "ice cream", "califlower", "lasagna"], false);
addEntry(["weekend", "cycling", "break", "beer"], true);
```


In statistics, corelation is displayed as a value in the range -1 to 1.
In this case, we can use _phi coefficient_.     
We take one event and put it in a frequency table like this:
```
No squirrel, no pizza : 76,
No squirrel, pizza : 9,
Squirrel, no pizza : 4,
Squirrel, pizza : 1
```

We use the formula to compute _phi_:    
_phi_ = n11 * n00 - n10 * n01 / square rt(n1 * n0 * n1 * n0)    

_n01_ indicates that the first variable is false (0) and second is true (1).
In the table above n01 is 9.
For the pizza table, the calculation would be :
```
1*76-4*9 / sqrt(5 * 85 * 10 * 80)
40 / sqrt(340000)
```
This means _phi_ is ~ 0.069.

A table can be an array and we can turn this into a function:
```js
function phi(table) {
	return (table[3] * table[0] - table[2] * table[1]) /
		Math.sqrt((table[2] + table[3]) *
			  (table[0] + table[1]) *
		 	  (table[1] + table[3]) *
			  (table[0] + table[2]));
}
```
```js
console.log(phi([76, 9, 4, 1]));
```
```
-> 0.068599434
```


To create a table from the original entries :
```js
function tableFor(event, journal) {
	let table = [0, 0, 0, 0];
	for (let i = 0; i < journal.length; i++) {
		let entry = journal[i], index = 0;
		if (entry.events.includes(event)) index += 1;
		if (entry.squirrel) index += 2;
		table[index] += 1;
	}
	return table;
}
```
```js
console.log(tableFor("pizza", JOURNAL));
```
```
-> [76, 9, 4, 1]
```



#### Array loops

There is an array _for_ loop:
```js
for (let entry of JOURNAL) {
	console.log("${entry.events.length} events.");
}
```

To compute the corelation for all events, we must first know all the events:
```js
function journalEvents(journal) {
	let events = [];
	for (let entry of journal) {
		for (ket event of entry.events) {
			if (!events.includes(event)) {
				events.push(event);
			}
		}
	}
	return events;
}
```
And calculate all corelations:
```js
for (let event of journalEvents(JOURNAL)) {
	console.log(event + " : ", phi(tableFor(event, JOURNAL)));
}
```
```
-> carrot :   0.0140970969
-> exercise : 0.0685994341
-> weekend :  0.1371988681
-> bread :   -0.0757554019
-> pudding : -0.0648203724
. . .
```

Filter corelations that are bigger then 0.1 or smaller then -0.1:
```js
for (let event of journalEvents(JOURNAL)) {
	let correlation = phi(tableFor(event, JOURNAL));
	if (correlation > 0.1 || correlation < -0.1) {
		console.log(event + " : ", correlation);
	}
}
```
```
-> weekend : 0.1371988681
-> brushed teeth : -0.3805211953
-> candy : 0.129607447
-> work : -0.1371988681
-> spaghetti : 0.2425356250
-> reading : 0.1106828054
-> peanuts : 0.5902679812
```

Now do:
```js
for (let entry of JOURNAL) {
	if (entry.events.includes("peanuts") &&
	    !entry.events.includes("brushed teeth")) {
		entry.events.push("peanut teeth");
	}
}
```
```js
console.log(phi(tableFor("peanut teeth", JOURNAL)));
```
```
-> 1
```


#### Other array methods

You can add and remove things at the start of an array using `shift` and `unshift`.
```js
let numbers = [1, 2 ,3 ,4 ,5];
console.log(numbers.shift());
console.log(numbers.unshift(0));
```
```
-> 2
-> 0
```

You can use `indexOf` to get the index of a value and `lastIndexOf`:
```js
console.log([1,2,3,2,1].indexOf(2));
console.log([1,2,3,2,1].lastIndexOf(2));
```
```
-> 1
-> 3
```

You can use `slice` to return an array to and from certain indexes:
```js
console.log([0,1,2,3,4].slice(2,4));
console.log([0,1,2,3,4].slice(2));
```
```
-> [2,3]
-> [2,3,4]
```

Combine arrays using `concat`:
```js
function remove(array, index) {
	return array.slice(0, index).concat(array.slice(index+1));
}
```
```js
console.log(remove(["a", "b", "c", "d"], 2));
```
```
-> ["a", "b", "d"]
```



#### String Properties


You can't add new properties to strings. 

You can slice strings and get index like this:
```js
console.log("coconut".slice(4,7));
console.log("coconut".indexOf("u"));
```
```
-> nut
-> 5
```
You can search index for more then one character:
```js
console.log("one two three".indexOf("ee"));
```
```
-> 11
```

The `trim` method removes whitespace (spaces, tabs, newlines)
```js
console.log("   ok".trim());
```
```
-> ok
```

You can pad with certain elements like this:
```js
console.log(String(6).padStart(3, "0"));
```
```
-> 006
```

Like Python, you can split at something:
```js
let words = sentance.split(" ");
```


You can also concatinate with `join` and add an element between them:
```js
sentance = words.join(". ");
```

You can repete a string:
```js
console.log("LA".repeat(3));
```
```
-> LALALA
```

Access elements of string like an array:
```js
let string = "abc";
console.log(string[1]);
```
```
-> b
```


#### Rest Parameters

In order to make a function that accepts any number of arguments, you can use the syntax :
```js
function max(...numbers) {
	let result = -Infinity;
	for(let number of numbers) {
		if (number > result) result = number;
	}
	return result;
}
```
```js
console.log(max(4,1,9,-2));
```
```
-> 9
```

If there are any parameters before it, they will not be in the array. 


You can use the same notation to _call_ a function with an array of arguments:
```js
let numbers = [5,1,8];
console.log(max(...numbers));
```

This passes elements as different aguments. You can also include along other arguments `max(9, ...numbers, 2)`. 

```js
let words = ["gonna", "give"];
console.log(["never", ...words, "you", "up"]);
```
```
-> ["never", "gonna", "give", "you", "up"]
```


#### The Math Object


`Math` has a lot of math reated utilities, including `max`, `min`, `sqrt`, etc. This is only one object that acts as a namespace so that the functinos are not global.

Javascript tells you when you declare two bindings with the same name only if they are declared with `let` or `const`, but not for `var` or `function`. 

Example of use:
```js
function randomPointOnCircle(radius) {
	let angle = Math.random() * 2 * Math.PI;
	return {x : radius * Math.cos(angle),
		y : radius * Math.sin(angle)};
}
```
```js
console.log(randomPointOnCircle(2));
```
```
-> {x : 0.3667, y : 1.966}
```

Random gives you a random number between 0 and 1. You can use `floor` to get a whole number instead of a fractional one:
```js
console.log(Math.floor(Math.random() * 10));
```
```
-> 2
```


#### Destructuring


This was the original _phi_ function:
```js
function phi(table) {
	return (table[3] * table[0] - table[2] * table[1]) /
		Math.sqrt((table[2] + table[3]) *
			  (table[0] + table[1]) *
			  (table[1] + table[3]) *
			  (table[0] + table[2]));
}
```
There is a better way to write this:
```js
function phi([n00, n01, n10, n11]) {
	return(n11 * n00 - n10 * n01) /
	       Math.sqrt((n10 + n11) * (n00 + n01) *
			 (n01 + n11) * (n00 + n10));
}
```

A similar thing works for objects:

```js
let {name} = {name : "Faraji", age : 23};
console.log(name);
```
```
-> Faraji
```

#### JSON

JSON looks similar to arrays and objects. With a few restrictions: 
* All property names are surrounded by double quotes,
* Only simple data expressions are allowed,
* No function calls or anything that involves computation,
* No comments.

An entry looks like this:
```
{
	"squirrel" : false,
	"events" : ["work", "touched tree", "pizza", "running"]
}
```


You use the functions `JSON.stringify` and `JSON.parse` to concert to and from this format.  The first returns a JSON-encoded string, the second takes such a string and converts it to the value it encodes.

```js
let string = JSON.stringify({squirrel: false,
			     evens: ["weekend"]});
console.log(string);
console.log(JSON.parse(string).events);
```
```
-> {"squirrel": false, "events": ["weekend"]}
-> ["weekend"]
```

