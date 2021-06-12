

### Functions and the power of abstract thinking



#### IIFEs and Asynchronous Code

```js
setTimeout(function() { console.log('hello'); }, 1500);
```
This prints out `hello` after 1.5 seconds.
The function delays the execution of the first argument by the second.


```js
var i;
for(i = 5; i >= 0; i--) {
	setTimeout(function() {
		console.log(i===0 ? "go" : i);
	}, (i-1)*1000);
}
```

This function does not print out _5 4 3 2 1 go_. It prints out -1 six times.      
This happens because the function passed to setTimeout does not start straight away. It will start in the furure. Therefore, setTimeout reaches -1 by that time. 

One way to fix this in the past has been:

```js
function loopBody(i) {
	setTimeout(function() {
		console.log(i===0 ? "go" : i)';
	}, (5-i)*1000);
}

var i;
for(i = 5; i > 0; i--) {
	loopBody(i);
}
```
The value of `i` is passed into the function. Because it is externally decremented, it prints out _5 4 3 2 1 go_.

This is where IIFE's come in. They are anonymous functions that are executed immediately:

```js
var i;
for(i = 5; i > 0; i--) {
	(function(i) {
		setTimeout(function() {
			console,log(i===0 ? "go" : i);
		}, (5 - i) * 1000);
	})(i);
}
```

This example can also be greatly simplified by using block-scoped variables, using _let_:

```js
for(let i = 5; i > 0; i--) {
	setTimeout(function() {
		console.log(i===0 ? "go" : i);
	}, (5-1) * 1000);
}
```



### Functions as Pipelines

Functions in arrays are useful for creating _pipelines_, bundles of functions that are called one after the other modifying the information:


```js
const sin = Math.sin;
const cos = Math.cos;
const theta = Math.PI/4;
const zoom = 2;
const offset = [1, -3];

const pipeline = [
	function rotate(p) {
		return {
			x: p.x * cos(theta) - p.y * sin(theta),
			y: p.x * sin(theta) + p.y * cos(theta)
		};
	},
	
	function scale(p) {
		return { x: p.x * zoom, y: p.y * zoom };
	},

	function translate(p) {
		return { x: o.x + offset[0], y: p.y + offset[1]; };
	}
];

// Use of pipeline

const p = { x: 1, y: 1 };
let p2 = p;

for(let i = 0; i< pipeline.length; i++) {
	p2 = pipeline[i](p2);
}

// p2 is now p rotated 45 degrees around the origin,
// moved 2 units further from the origin
// and translated 1 unit to the right and 3 units down.
```




#### Passing functions into functions


A common way to achieve asunchronous execution is to pass a function (called a _callback_ or _cb_) into another function that is envoked when the enclosing function is done. 

Another example might be:


```js
function sum(arr, f) {
	if(typeof f != 'function') f = x => x;
	return arr.reduce((a, x) => a += f(x), 0);
}

sum([1,2,3]);						// 6
sum([1,2,3], x => Math.pow(x, 3));	// 36
```


