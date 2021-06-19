

### Object Property Configuration and Proxies


There are two types of properties: _data properties_ and _accessor properties_.

_Accessor_ properties have both a _getter_ and a _setter_.

If we had a User class with an `email` variable, we could do:


```js
const USER_EMAIL = Symbol();		// so that is we do not access it by mistake
class User {
	set email(value) {
		if(!/@/.test(value)) throw new Error('Invalid email: ${value}');
		this[USER_EMAIL] = value;
	}
	
	get email() {
		return this[USER_EMAIL];
	}
}
```

This allows us to do this:
```js
const u = new User();
console.log(u.email);
```

Instead of the classic:

```js
console.log(u.getEmail);
```

The two methods are bundled into a single property `email`. The appropiate one is called depending on the arguments.

You can also provide a getter without a setter, or only a setter:

```js
class Reclangle {
	constructor(width, height) {
		this.width = width;
		this.height = height;
	}
	get perimeter() {
		return this.width*2 + this.height*2;
	}
}
```


#### Object Property Attributes

Properties also have _attributes_. To see them, we can do:


```js
const obj = { foo: "bar" };
Object.getOwnPropertyDescriptor(obj, 'foo');
```
```
{ value: "bar", writtable: true, enumerable: true, configurable: true }
```

__Writtable__ controls whether it can be changed.
__Enumerable__ controls whether it will be enumerated when the properties are enumerated (for...in, Object.keys).
__Configurable__ controls weather it can be deleted or have the attributes modified.


TODO: Table


We can modify or create properties with `Object.defineProperty`:

```js
Object.defineProperty(obj, 'foo', { writable: false });
```
```js
obj.foo = 3;		// TypeError
```

Adding properties is useful because there is no way to add an accessor property after an object has been created:

```js
Object.defineProperty(obj, 'color', {
	get: function() { return this.color; },
	set: function(value) { this.color = value; };
});
```

To create a data property, you have to provide the `value` property:

```js
Object.defineProperty(obj, 'name', {
	value: 'Cynthia'
});

Object.definePropetry(obj, 'greet', {
	value: function() { return `Hello, my name is ${this.name }.`};
});
```

Another use for this is to hide properties from being enumerated:

```js
const arr = [3, 1.5, 9, 2, 5.2];
arr.sum = function() { return this.reduce((a, x) => a + x); }
arr.avg = function() { return this.sum()/this.length; }

Object.definePropetry(arr, 'sum', { enumerable: false });
Object.defineProperty(arr, 'avg', { enumerable: false });
```

You can also do this the CHAD way:

```js
const arr = [3, 1.5, 9, 2, 5.2];

Object.defineProperty(arr, 'sum', {
	value: function() { return this.reduce((a, x) => a + x); }
	enumerable: false;
});
```

There is also an `Object.defineProperties` (plural), to define multiple:

```js
const arr = [3, 1.5, 9, 2, 5.2];

Object.defineProperty(arr, 'sum', {
	sum: {
		value: function() { return this.reduce((a, x) => a + x); },
		enumerable: false;
	},
	avg: {
		value: function() { return this.sum()/this.length; },
		enumerable: false
	}
);
```



#### Protecting Objects: Freezing, Sealing and Preventing Expansion

JS provides three mechanism for protecting agains unintentional modifications:

__Freezing__ prevents _any_ changes to an object. It makes it immutable. To freeze an object:

```j
const appInfo = {
	company: 'White Knight Software, Inc.',
	version: '1.3.5',
	buildId: '0a995448-ead4-4a8b-b050-9c9083279ea2',
	copyright() {
		return `@ ${new Date().getFullYear()}, {this.company}`; 
	}
};
```
```js
Object.freeze(appInfo);
Object.isFrozen(appInfo);	// True
```


__Sealing__ an object prevents the addition of new properties, or the reconfiguration or removal of existing ones:

```js
class Logger {
	constructor(name) {
		this.name = name;
		this.log = [];
	}
	add(entry) {
		this.log.push({
			log: entry,
			timestamp: Date.now()
		});
	}
}
```
```js
Object.seal(log);
Object.isSealed(log);		// True
```
```js
log.name = . . .;
log.add( . . . );
log.newProp = . . .;	// TypeError
delete log.name;		// TypeError
```


Making an object __nonextensible__ only prevents new properties from being added. Existing ones can be assigned to, deleted or reconfigured:

```js
const log2 = new Logger('CoolLogger');
Object.preventExtension(log2);
Object.isExtensible(log2);		// True
```
```js
log2.name = . . .
log2.add( . . . )
log2.newProp = . . . 	// Error
```



#### Proxies


An object proxy has the ability to intercept and modify actions on an object.

```js
const coefficients = {
	a: 1,
	b: 2,
	c: 3
};
```
Used like this:
```js
function evaluate(x, c) {
	return c.a + c.b * x + c.c * Math.pow(x, 2);
}
```

If we pass into this function an object with missing coefficients, it returns `NaN`. 

Because proxis can intercept actions against an object, we can make sure that any undefined property recieves a value of 0.

```js
const betterCoefficients = new Proxy(coefficients, {
	get(target, key) {
		return target[key] || 0;
	}
});
```

The `get` function takes 3 arguments: target, property key and the reciever (proxy itself or something that derrives from it). 

```js
betterCoefficients.a; // 1
betterCoefficients.b; // 2
betterCoefficients.c; // 3
betterCoefficients.d; // 0
betterCoefficients.anything; // 0;
```

We can modify out proxy to only proxy single lowercase letters:

```js
const betterCoefficients = new Proxy(coefficients, {
	get(target, key) {
		if(!/^[a-z]$/.test(key)) return target[key];
		return target[key] || 0;
	}
});
```

We can also intercept properties or accessors from being set with the `set` handler. Let's consider an example where we have dangerous properties on an object. We want to prevent them from being set, and the methods from being called, without an extra step:


```js
const cook = {
	name: "Walt",
	redPhosphorus: 100,		// dangerous
	water: 500				// safe
};

const protectedCook = new Proxy(cook, {
	set(target, key, value) {
		if(key === 'redPhosphorous') {
			if(targer.allowDangerousOperations)
				return target.redPhosphorous = value;
			else
				return console.log("NO");
		}
		target[key] = value;
	}
});
```



