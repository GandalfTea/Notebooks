

### Exceptions and Error Handling

&nbsp;

#### The _Error_ Object

```js
const err = new Error('Invalid Email');
```
Typically used like:

```js
function validateEmail(email) {
	return email.match(/@/) ?
		email:
		new Error('Invalid Email: ${email}');
}
```
To call the error, we can use `typeof` or `instanceOf`:

```js
const validateEmail = validateEmail(email);

if(validateEmail instanceof Error) {
	console.error(`Error: ${validateEmail.message}`);
} else {
	console.log(`Valid Email: ${validatedEmail}`);
}
```

&nbsp;

#### Try and Catch

To rewrite the previous example:

```js

try {
	const validateEmail = validateEmail(email);
	if(validatedEmail instanceof Error) {
		console.error(`Error: ${validateEmail.message}`);
	} else {
		console.log(`Valid Email: ${validatedEmail}`);
	}
} catch(err) {
	console.log(`Error: ${err.message}`);
}
```
This way you can safeguard agains both known errors, and also unexpected errors.    
In this case, it would be wrong type of input.

&nbsp;

#### Throwing 

You can also create and raise errors yourself.   
You can throw anything, but it usually is an instance of _Error_.

```js
function billPay(amound, payee, account) {
	if(amount > account.balance)
		throw new Error("Insufficient funds");
	account.transfer(payee, amount);
}
```

&nbsp;

#### Try...Catch...Finally


This helps when you might want to release a resource without it being tied up in the faulty code.   
We do not want to free it in the catch block, because it would not be released if there is no error.   

```js
try {
	throw new Error ('whoops');
}catch (err) {
	// . . .
} finally {
	// This is always executed.
	// Perform cleanup here
}
```




