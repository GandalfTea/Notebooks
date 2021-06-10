

### Array Processing

&nbsp;
	        

#### Adding or Removing

```js
const arr = ['b', 'v', 'd'];

arr.push('e');				// adds 'e' at the end		returns 4
arr.pop();				// pops last element		returns 'e'
arr.unshift('a');			// adds 'a' at the front	returns 4
arr.shift();				// removes 'a'.			returns 'a'
```

&nbsp;

#### Concatination

```js
arr.concat(4,5,6);
```
This creates a new array, `arr` is unmodified.

&nbsp;

#### Slice

```js
const arr = [1, 2, 3, 4, 5];

arr.slice(3);		// returns [4, 5]
arr.slice(2,4);		// returns [3, 4]
arr.slice(-2);		// returns [4, 5]
```
Negative arguments refer to the end of the string.

&nbsp;

#### copyWithin

```js
const arr = [1, 2, 3 ,4];

arr.copyWithin(1, 2);		// arr is now [1, 3, 4, 4]		
arr.copyWithin(2, 0, 2);	// arr is now [1, 3, 1, 3]  ( this starts from [1, 3, 4, 4] )
arr.copyWithin(0, -3, -1);	// arr is now [3, 1, 1, 3]
```
First argument is where to copy to.
Second argument is where to start copying from.
Third optional argument is where to stop coying from.

&nbsp;

#### Fill

```js
const arr = new Array(r).fill(1);			// [1, 1, 1, 1, 1]
arr.fill('a');								// ['a', 'a', 'a', 'a', 'a']
```

&nbsp;

#### Sort


```js
const arr = [1, 2, 3, 4, 5];

arr.reverse();		// [5, 4, 3 ,2, 1]
```

```js
const arr = [3, 2, 4, 5, 1];

arr.sort();		// [1, 2, 3, 4, 5]
```

Within `sort`, you can specify a sort function:


```js
const arr = [
	{ name: 'Suzanne' },
	{ name: 'Jim' },
	{ name: 'Trevor' },
	{ name: 'Amanda' }
];

arr.sort();									// arr unchanged

arr.sort((a, b) => a.name > b.name);		// sorted alphabetically

arr.sort((a, b) => a.name[1] < b.name[1]);	// sorted reverse-aplhabetically
```

&nbsp;

#### Searching

They all return -1 of the search was unsuccessful.

```js
const arr = [1, 5, 'a', o, true, 5, [1, 2]. '9'];


arr.indexOf(5);
arr.lastIndexOf(5);
```

```js
const arr = [
	{ id: 5, name: 'Bob' },
	{ id: 7, name: 'Francis' }
];

arr.findIndex( o => o.id === 5);	// returns index
arr.findIndex( 0 => 0 === 3);		// returns -1

arr.find( o => o.id === 5); 		// returns object
arr.find( 0 => o.id == 2);			// null
```

&nbsp;

#### map and filter

`map` transforms all the elements:

```js
const cart = [
	{ name: 'Widget', price: 9.95 },
	{ name: 'Gadget', price: 22.95}
];

const names = cart.map( x => x.name );
const prices = cart.map( x => x.price );
const promption = cart.map( x => x.price*0.8);
const lcNames = names.map(String.toLowerCase);
```
```js
const items = ['Widget', 'Gadget'];
const prices = [9.95, 22.95];
const cart = items.map((x, i) => ({ name: x, price: prices[i]}));
// this works because when an array is called, it gets called with the element itself, index and array itself
// therefore, i is set to the index automatically.
// parantheses so that it is not read as a block
```

`filter` filters by a function you pass into it.

```js
const arr = [1, 2, 3, 4, 5];

arr.filter(x => x === 5);
```

&nbsp;

#### reduce

This is used to transform the entire array. It is called reduce because it is usually used to reduce the entire array into a single value. For example, average or summing.
You provide the function.

```js
const arr = [5, 7, 2, 4];
const sum - arr.reduce((a, x) => a += x, 0);	// starting from zero
```

You can also use objects as the accumulator:
The accumulator checks for properties of the same name as the element

```js
const words = ['Beachball', 'Rodeo', 'Angel',
	'Aardvark', 'Xylophone', 'November', 'Chocolate',
	'Papaya', 'Uniform', 'Joker', 'Clover', 'Bali'];

const alphabetical = words.reduce( (a, x) => {
	if(!a[x[0]]) a[x[0]]= [];
	a[x[0]].push(x);
	return a; }, {});
```

Another example:
Mean and variation of dataset:

```js
const data = [3.3, 5, 7.2, 12, 4, 6, 10.3];

// Donald's Knuth's algorithm for calculating variance:
const stats = data.reduce((a, x) => {
		a.N++;
		let delta = x - a.mean;
		a.mean += delta/a.N;
		a.M2 += delta*(x - a.mean);
		return a;
	}, {N: 0, mean: 0, M2: 0});

if(stats.N > 2) {
	stats.variance = stats.M2 / (stats.N - 1);
	stats.stdev = Math.sqrt(stats.variance);
}
```

#### Deleted or Never-Defined elements

map, filter and redice do not envoke the function for elements that have never been asigned or deleted. 

&nbsp;

#### String joining

```js
const arr = [1, null, 'hello', 'there', true, undefined];
delete arr[3];

arr.join();			// "1,,hello,true,"
arr.join('');		// "1hellotrue"
arr.join(' -- ')	// "1 -- -- hello -- -- true --"
```

It can be used to create HTML < ul> elements:

```js
const attributes = ['Nimble', 'Perceptive', 'Generous'];

const html = '<ul><li>' + attributes.join('</li><li>') + '</li></ul>';

// html: "<ul><li>Nimble</li><li>Perceptive</li><li>Generous</li></ul>"


