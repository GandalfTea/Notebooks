


## Higher-Order Functions


You can use the `=>` function declaration like a _lambda_:
```js
let labels = [];
repeat(5, i => {
	labels.push("Unit ${i + 1}");
});
```
```js
console.log(labels);
```
```
-> ["unit 1", "Unit 2", "Unit 3", "Unit 4", "Unit 5"]
```


You can also have functions that change other functions:
```js
function noisy(f) {
	return(...args) => {
		console.log("Calling with", args);
		let result = f(...args);
		console.log("Called with", args, ", returned", result);
		return result;
	};
}
```
```js
noisy(Math.min)(3, 2, 1);
```
```
-> Calling with [3, 2, 1]
-> Called with [3, 2, 1], returned 1
```

#### Script Data Set

We have a data set about scripts, in all sorts of languages like Latin, Unicode, Arabic, etc. 

Every script has some data about it:
```
{
	name: "Coptic",
	ranges: [[994, 1008], [11392, 11508], [11513, 11520]],
	direction: "ltr",
	year: -200,
	living: false,
	link: "http://en.wikipedia.org/wiki/Coptic_alphabet"
}
```
`ranges` refers to the Unicode ranges assigned to it.     
`direction` means the direction of reading, in this case, left-to-right.   

To find all the scripts in the data set that are still in use, we can do:
```js
function filter(array, test) {
	let passed = [];
	for(let element of array) {
		if (test(element)){
			passed.push(element);
		}
	}
	return passed;
}
```
```js
console.log(filter(SCRIPTS, script => script.living));
```
This is already a standard array method.



The `map` method applies a function to all elements of an array and returns a new array with the values. 
```js
function map(array, transform) {
	let mapped = [];
	for (let element of array) {	
		mapped.push(transform(element));
	}
	return mapped;
}
```
```js
let rtlScripts = SCRIPTS.filter(s => s.direction == "rtl");
console.log(map(rtlScripts, s => s.name));
```

This is also a standard array method.


The function `reduce` (also called `fold`) builds a value by repeatedly taking a single element from an array and combining it with the current value:
```js
function reduce(array, combine, start) {
	let current = start;
	for (let element of array){
		current = combine(current, element);
	}
	return current;
}
```
```js
concole.log(reduce([1, 2, 3, 4], (a,b) => a + b, 0);
```
```
-> 10
```

To find the average year of origin, we need to compose operations:
```js
function average(array) {
	return array.reduce((a,b) => a+b) / array.length;
}
```
```js
console.log(Math.round(average(
	SCRIPTS.filter(s => s.living).map(s => s.year))));
```
```
-> 1165
```
```js
console.log(Math.round(average(
	SCRIPTS.filter(s => !s.living).map(s => s.year))));
```
```
-> 204
```


In order to go through the data set and figure out what script a piece of text is using, we can do this:
```js
function characterScript(code) {
	for(let script of SCRIPTS) {
		if(script.ranges.some(([from, to]) => {
			return code >= from && code < to;
		})) {
			return script;
		}
	}
	return null;
}
```
```js
concole.log(characterScript(121));
```
```
-> {name: "Latin", ...}
```

To count the characters belonging to each script:
```js
function countBy(items, groupName) {
	let counts = [];
	for (let item of items) {
		let name = groupName(item);
		let known = counts.findIndex(c => c.name == name);
		if (known == -1) {
			counts.push({name, count: 1});
		} else {
			counts[known].count++;
		}
	}
	return counts;
}
```
```js
console.log(countBy([1, 2, 3, 4, 5]. n => n > 2));
```
```
-> [{name: false, count: 2}, {name: true, count: 3}]
```

`findIndex` finds the first value for the function input.


Using this, we can rewrite the function `textScript`:

```js
function textScript(text) {
	let scripts = countBy(text, char => {
			let script = characterScript(char.codePointAt(0));
			return script ? script.name : "none";
		      }).filter(({name}) => name != "none");
	let total = scripts.reduce((n, {count}) => n + count, 0);
	if (total == 0) return "No scripts found.";

	return scripts.map(({name, count}) => {
			return "${Math.round(count*100 / total)} % ${name}";
			}).join(", ");
}
```
```js
console.log(textScripts('英国的狗说"woof", 俄罗斯的狗说"тяв"'));
```
```
-> 61% Han, 22% Latin, 17% Cyrillic
```
