

### Functions 

&nbsp;

#### Destructuring Arguments

You can destruct arguments into individual variables:
```js
function getSentance( {subject, verb, object }) {
	return `${subject} ${verb} ${object}`;
}

const o = {
	subject: "I",
	verb: "am",
	object: "your father."
};

const arr = ["I", "do", "hard drugs."]

getSentance(o); 	// I am your father.
getSentance(arr); 	// I do hard drugs.
```

&nbsp;
            
       
#### Spread Operator

You can use this operator ( ... ) to collect any additional arguments:

```js
function addPrefix(prefix, ...words) {
	const prefixedWords = [];
	for(let i=0; i<words.length; i++) {
		prefixedWords[i] = prefix + words[i];
	}
	return prefixedWords;
}

addPrefix("con", "verse", "vex");
```

&nbsp;

#### Default Arguments

```js
function f(a, b = 'default', c = 3){ . . . }
```

&nbsp;


#### Fat Arrow

The following two are the same:

```js
const f = function() { return "hello"; }
const f = function(name) { return "hello, ${name}"; }
const f = function(a, b) { return a + b; }
```

```js
const f = () => "hello";
const f = name => "Hello, ${name}";
const f = (a, b) => a + b;
```


&nbsp;

#### this

Normally, the _this_ keyward refers to an object method, meaning the object calling. 
There are more uses in Javascript.

The scope of the keyward is confusing. What if we reasign the function that calls it?

The following code has this problem:

```js
const o = {
	name: 'Julie',
	greetBackwards: function() {
		function getReverseName() {
			let nameBackwards = '';
			for(let i = this.name.length - 1; i >= 0; i--) {
				nameBackwards += self.name[i];
			}
		return nameBackwards;
		}
	return `${getReverseName()} si eman ym, olleH`;
	}
};

o.greetbackwards();
```

getReverseName has access to the keyward byt the meaning is lost by _greetBackwards_.

To solve this, we simply need to assign it to a var while still in greetBackwards:

```js
const self = this;
```

&nbsp;



#### Another use for _this_

You can write a function that is not a member method and still call it on an object:

```js
function greet() {
	return `Hello, I'm ${this.name}!`;
}
```
```
greet();				// this is not bound to anything.
greet.call(bruce);		// Hello, I'm Bruce!
```

The first value passed into such a function is the object.

```js
function update(birthYear, occupation) {
	this.birthYear = birthYear;
	this.occupation = occupation;
}
```
```js
update.call(bruce, 1949, 'singer');		// first argument is object
```

Another way to do this is to use `apply`. The only difference is that it takes arguments as an array:

```js
update.apply(bruce, [1955, 'actor']);
```

This is useful for passing an array as arguments. For example, finding biggest number.

```js
const arr = [2,3,-5,15,6];
Math.min.apply(null, arr);
```
Or, you can use the spread opperator:
```js
Math.min(...arr)
```


Another way to use functions like this is to use `bind`. This permanently binds an object to the _this_ keyward for the function.

```js
const updateBruce = update.bind(bruce);

updateBruce(1904, 'actor');
```

You can also provide parameters to bind:
```js
const updateBruce1949 = update.bind(bruce, 1949);

updateBruce1949('singer', 'songwriter'); 	// Bruce is now { name: Bruce, birtyear: 1949 
						// occupation: 'singer, songwriter'} 
```




