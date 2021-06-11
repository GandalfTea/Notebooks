

### Maps and Sets

&nbsp;

#### Map

```js
const userROles = new Map();

userRoles.set(u1, 'User');
userRoles.set(u2, 'User');
userRoles.set(u3, 'Admin');
```

This can also be chained:

```js
userRoles
	.set(u1, 'User'),
	.set(u2, 'User'),
	.set(u3, 'Admin');
```

You can also pass a 2D array to the constructor:

```js
const userRoles = new Map([
	[u1, 'User'],
	[u2, 'User'].
	[u3, 'Admin']
]);
```


You can ask for the value using the `get()` method.      
You can check the value using the `has()` method.     
You can change the value using the `set()` method.      

```js
userRoles.has(u1);			// True
userRoles.get(u1);			// 'User'
userRoles.set(u1, 'Admin');
```
If the key has no value, or it does not exist, it returns _undefined_.


You can check the size of the map using the `size()` method:

```js
userRoles.size();	// 3
```

To get the whole of the map, you can use:      
`.keys()` to get the keys,      
`.values()` to get the values,      
`.entries()` to get the entries as arrays, with the first value being the _key_ and second _value_.

&nbsp;

#### Weak Maps

Same as maps, but the keys must be objects.         
Keys can be garbage-collected.       
It cannot be iterated or cleared.   

Normally, JS keeps an object in memory as long as there is a reference to it. This is not true in Weak Maps.      
Thanks to these properties, it can be used to store private keys in object instances:


```js
const SecretHolder = (function() {

	const secrets = new WeakMap();

	return class {

		setSecret(secret) {
			secrets.set(this, secret);
		}
		
		getSecret() {
			return secrets.get(this);
		}
	}

})();
```

Here, we put the WeakMap inside a IIFE (_Immediately Invoked Function Expression_) which runs as soon as it is defined.          

Note :        

Syntaxfor IIFE looks like this:

```js
(function () {
	statements
})();
```

Because of this, we can only get the secret using the get method.      
If we used a normal Map, the SecretHolder objects could never be garbage-collected.

&nbsp;


#### Sets


A set cannot contain duplicate data.      
Developing on out previous example:

```js
const roles = new Set();

roles.add("User");
roles.add("Admin");
roles.size;				// 2

// If we try to add the same thing again, nothing changes:
roles.add("User");
roles.size;				// 2
```
You can delete:

```js
roles.delete("Admin");
roles.size;				// 1
```

&nbsp;


#### Weak Sets


It can only contain objects, and they are garbage-collected. 
It cannot be iterated. It is not very useful overall.



