

## Values, Types and Operators

### Values
#### Numbers

You can use `e` to represent exponents:
```js
2.998e8
```
is 2,998 x 10^8 = 299800000

There are 3 special numbers:
`Infinity` which represents positive infinity value.
`-Infinity` negative infinity value.
`Nan` Not A Number. Meaning even if it is a value of number type, error. For trying to do 0 / 0 or Infinity - Infinity.

#### Strings

You can use backticks, single or double quotes.
```js
`String`
'String'
"String"
```
You can use `\n` for newline in string.
Concatination is like Python.

You can insert info into a string like this:
```js
"half of 100 is ${100 / 2}"
```
#### Unary 
You can use `typeof` to get the type of a value.
```js
console.log(typeof 4.5)
```
```
-> number
```

#### Boolians


Like Python.

#### Logical Operators

`&&` is AND
`||` is OR
`!` is NOT

Also the simple if statement:
```js
console.log(true ? 1 : 2);
console.log(false ? 1 : 2);
```
```
-> 1
-> 2
```
#### Automatic conversion

Basically Python
```js
console.log(8 * null)
-> 0
console.log("5" - 1)
-> 4
console.log("5" + 1)
-> 51
console.log("five" * 2)
-> Nan
console.log(false == 0)
-> true
```

