

### Iterators and Generators

&nbsp;


#### Iterators



You can iterate through an array:

```js
const book = [
	"Twinkle, twinkle, little bat!",
	"How I wonder what you're at!",
	"Up above the world you fly,",
	"Like a tea tray in the sky.",
	"Twinkle, twinkle, little bat!",
	"How I wonder what you're at!",
];
```
```js
const it = book.values();
let current = it.next();
while(!current.done) {
	console.log(current.value);
	current = it.next();
}
```

You can make your object iterable by adding a _Symbol.iterator_ to it:

```js
class Log {
	constructor() {
		this.messages = [];
	}
	add(message) {
		this.messages.push({ message, timestamp: Date.now() });
	}
	[Symbol.iterator](){
		return this.messages.values();
	}
}
```
```js
const log = new Log();
log.add('first');
log.add('second');
log.add('third');

for(let entry of log) {
	console.log(`${entry.message} @ ${entry.timestamp}`);
}
```


Another example, Fibonacci sequence:

```js
class Fibonacci {
	[Symbol.iterator]() {
		let a = 0, b = 1;
		return {
			next() {
				let rval = { value: b, done: false};
				b += a;
				a = rval.value;
				return rval
			}
		};
	}
}
```

&nbsp;

#### Generators

Generators are functions that allow you to control the execution by using iterators.     

It differs from a normal function by:
* It can give control back to the caller at any point.
* It does not run right away when it is called, it returns an iterator and the function runs when you call the iterator's `next` method.

```js
function* rainbow() {
	yield 'red';
	yield 'orange';
	yield 'yellow';
	yield 'green';
	yield 'blue';
	yield 'indigo';
	yield 'violet';
}
```
```js
const it = rainbow();
it.next(); 	// { value: 'red', done: false }
it.next(); 	// { value: 'orange', done: false }
...
it.next(); 	// { value: 'undefined', done: true }
```
```js
for(let color of rainbow()) {
	console.log(color);
}
```


The communication with the function happens in both ways. Here is an example that can talk:

```js
function* interrogate(){
	const name = yield "What is your name?";
	const color = yield "What is your favorite color?";
	return `${name}'s favorite color is ${color}.`;
}
```
```js
const it = interrogate();
it.next();			// { value: "What is your name", done: false }
it.next("Ethan");	// { value: "What is your favourite color?", done: false }
it.next("Orange")	// { value: "Ethan's favourite color is orange.", done: true }
```

_yield_ does not end the generator. You have to call _return_ for `done` to be True.





