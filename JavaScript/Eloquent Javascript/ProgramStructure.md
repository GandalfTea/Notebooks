
## Program Structure

&nbsp;

### Expressions and Statements

&nbsp;

#### Binding variables

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

#### Control Flow


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

&nbsp;


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

#### Comments

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

