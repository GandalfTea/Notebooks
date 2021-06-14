

### Asynchronous Programming

&nbsp;


The primary things that are done are:
* Network requests.
* Filesystem operations.
* Intentionally time-delayed functionality.

Javascript is a single threaded language.

&nbsp;


#### Callbacks

Callbacks are normal functions. 

You can use stuff like `setTimeout` to change the running flow of the function. For example, if you call a setTimeout in the middle of the function, the rest of the function will run, and after the timeout, the code within the block will run.

There are also `setInterval` and `clearInterval`.

`setInterval` runs the interval forever, or until you call `clear Intervak`.   

```js
const start = new Date();
let i = 0;
const intervalId = setInterval(function() {
	let now = new Date();
	if(now.getMinutes() !== start.getMinutes() || ++i > 10)
		return clearInterval(intervalId);
	console.log(`${i}: ${now}`);
}, 5*1000);
```
&nbsp;

#### Scope and Asynchronous Execution


Remember the example:

```js
function countdown() {
	let i;
	console.log("Countdown:");
	for(i = 5, i >= 0; i--) {
		setTimeout(function() {
			console.log(i===0 ? "GO!" : i);
		}, (5-i)*1000);
	}
}

countdown();
```

This does not print the countdown, but `-1` six times.      

The mistake here is declaring `i` outside of scope of the `for` loop.      

The problem happens inside the asynchronous function passed into the `setTimeout`.      

By the time the print is executed, the variable already reaches -1, at the end of the loop.

The call for each number, from 5 to 1 is executed immediately. The time function decides the time delay for each depending on the value it has. If the time is set to 1s, all execute at the same time. 

The function only reads the value of `i` when the print has to happen. 

We can solve this problem by moving the declaration of `i` in scope or using a IIFE function.      

&nbsp;




#### Error-First Callbacks

Because callbacks make exception-handeling harder,a convention emerged to use the first argument to a callback to recieve an error object. If that error object is _null_ or _undefined_, no error has occured.

```js

const fs = require('fs');

const fname = 'may_or_may_not_exist.txt';
fs.readFile(fname, function(err, data) {
	if(err) return console.log(`error reading file ${fname}: ${err.message}`);
	concole.log(`@{name} contents: ${data}`);
});
```

&nbsp;

#### Callback Hell

Callbacks can be nested making it time consuming to wait for each.

Error handling is also a problem.

```js
const fs = require('fs');

function readSketchyFile() {
	try {
		fs.readFile('thing.txt', function(err, data) {
			if(err) throw err;
		});
	} catch(err) {
		console.log('error report');
	}
}

readSketchyFile();
```

This will not catch the error because _try...catch_ blocks only work within the same function. The block is outside the function that throws the error.


&nbsp;


#### Promises

They attempt to solve some of the problems of callbacks.

When you call a promise-based async function, it returns a _Promise_ instance that can only either be _fulfilled_ or _rejected_.

```js
function countdown(seconds) {
	return new Promise(function(resolve, reject) {
		for(let i = seconds; i >= 0; i--) {
			setTimeout(function() {
				if (i === 13) return reject(new Error("ABANDON SHIP"));
				if(i > 0) console.log(i + '...');
				else resolve(console.log("GO"));
			}, (seconds-i)*1000);
		}
	});
}
```
```js
// this can also be cast into a variable.
countdown(5).then(
	function() {
		console.log("Successful");
	},
	function(err) {
		console.log("Error");
	}
);
```
&nbsp;

#### Events


Events emit events happening in the function, and anyone who wants to listen to them can throught a callback.    

Node provides a built-in emitter. jQuery in the browser also has an event mechanism.    

To include events in the countdown:

```js

const EventEmitter = require('events').EventEmitter;

class Countdown extends EventEmitter {
	constructor(seconds, superstitious) {
		super();
		this.seconds = seconds;
		this.superstitious = !!superstitious;  // the !! casts the variable to a boolian
	}
	
	go() {
		const countdown = this;
		return new Promise(function(resolve, reject) {
			for(let i = countdown.seconds; i >= 0; i--) {
				setTimeout(function() {
					if(countdown.superstitious && i === 13)
						return reject(new Error("ABORT ABORT"));
					countdown.emut('tick', i);
					if(i===0) resolve();
				}, (countdown.seconds - i) * 1000);
			}
		});
	}
}
```

Notice that we needed to save the value of _this_ inside of `countdown`. This is because _this_ would not have the same value inside the callback. 

We would use the countdown like this:

```js

const c = new Countdown(5);
	.on('tick', function(i) {
		if(i > 0) console.log(i + '...');
	});

c.go()
	.then(function() {
		console.log("GO");
	})
	.catch(function(err) {
		console.error(err.message);
	})
```

In order to skip the number 13, we have to clear all the pending timeouts:


```js
const EventEmitter = require('events').EventEmitter;

class Countdown extends EventEmitter {

	constructor(seconds, superstitious) {
		super();
		this.seconds = seconds;
		this.superstitious = !!superstitious;
	}

	go() {
		const countdown = this;
		const timeoutIds = [];
		return new Promise(function(resolve, reject) {
			for(let i=countdown.seconds; i>=0; i--) {
				timeoutIds.push(setTimeout(function() {
					if(countdown.superstitious && i===13) {

						// clear all pending timeouts
						timeoutIds.forEach(clearTimeout);

						return reject(new Error("DEFINITELY NOT COUNTING THAT"));
					}
					countdown.emit('tick', i);
					if(i===0) resolve();
				}, (countdown.seconds-i)*1000));
			}
		});
	}
}
```

&nbsp;


##### Promise Chaining

```js

function launch() {
	return new Promise(function(resolve, reject) {
		setTimeout(function() {
			resolve('In orbit');
		}, 2*1000);
	});
}
```

```js
const c = new Countdown(5)
	.on('tick', i => console.log(i + '...'));

c.go()
	.then(launch)
	.then(function(msg) {
		console.log(msg)
	})
	.catch(function(err) {
		console.error('Houston, we have a problem...');
	})
```


One of the advantage of this is that you do not need to catch errors at every step. The chain will stop and fall through to the _catch_ handler.

&nbsp;

#### Preventing Unsettled Promises

There is no error if you do not call either `resolve` or `reject`.      
One way to bypass this is to set a timeout for promises. If it is not settled in some amount of time, reject it.     
Let's modify the `launch` function to fail half the time:

```js
function(launch) {
	return new Promise(function(resolve, reject) {

		if(Math.random() < 0.5) return;

		setTimeout(function() {
			resolve('In orbit');
		}, 2*1000);
	});
}
```

Notice we are not calling `reject`. We fail silently.         
A function to add a timeout would be:

```js
function addTimeout(fn, timeout) {
	if(timeout === undefined) timeout = 1000;	// Default
	return function(...args) {
		return new Promise(function(resolve, reject) {
			const tid = setTimeout(reject, timeout,
				new Error('Timed out.'));
			fn(...args)
				.then(function(...args) {
					clearTimeout(tid);
					resolve(...args)
				})
				.catch(function(...args) {
					clearTimeout(tid);
					reject(...args);
				});
		})
	}
}
```

```js
c.go()
	.then(addTimeout(launch, 4*1000))
	.then(function(msg) {
		console.log(msg);
	})
	.catch(function(err) {
		console.log("Problem: " + err.message);
	});
```

&nbsp;


#### Generators


Generators make async a little easier to understand.

We'll turn Node's error-first callbacks into promises and encapsulate it into a function `nfcall`:

```js
function nfcall(f, ...args) {
	return new Promise(function(resolve, reject) {
		f.call(null, ...args, function(err, ...args) {
			if(err) return reject(err);
			resolve(args.length < 2 ? args[0] : args);
		});
	});
}
```

This method already exists in the _Q promise library_, with the same name.

We'll also need `setTimeout` which takes a callback:

```js
function ptimeout(delat) {
	return new Promise(function(resolve, reject) {
		setTimeout(resolve, delay);
	});
}
```

Because generators are not inherently async, the next thing we need is a _generator runner_ that will manage the communication:

```js
function grun(g) {
	const it = g();
	(function iterate(val) {
		const x = it.next(val);
		if(!x.done) {
			if(x.val instanceof Promise) {
				x.value.then(iterate).catch(err => it.throw(err));
			} else {
				setTimeout(iterate, 0, x.value);	
			}
		}
	})();
}
```




