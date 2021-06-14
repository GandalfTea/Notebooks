
### Math


All numbers in JS are [IEEE 754 64-bit floats](https://en.wikipedia.org/wiki/Double-precision_floating-point_format ). For practical purposes, you can think about numbers in JS as _Real_ numbers. The _Math_ object included contains most common functions. For more complex structures or algorithms, there is _Math.js_.  

&nbsp;


#### Formatting numbers

```js
x = 19.51;
```
```js
x.toFixed(3);		// 19.501
x.toFixed(1);		// 19.5
x.toFixed(0);		// 20
```
&nbsp;

```js
x = 3800.5;
```
```js
x.toExponential(4);	// 3.8005e+3
x.tpExponential(1);	// 3.8e+3
x.toExponential(0);	// 4e+3
```
&nbsp;


```js
x = 1000;
```
```js
x.toPrecision(5);	// 1000.0
x.toPrecision(3);	// 1.00e+3
x.toPrecision(1);	// 1e+3
```
&nbsp;

```js
x = 15.335;
```
```js
x.toPrecision(6);	// 15.3350
x.toPrecision(3);	// 15.3
x.toPrecision(2);	// 15
x.toPrecision(1);	// 2e+1
```
&nbsp;

```js
x = 12;
```
```js
x.toString();     // "12"  (base 10)
x.toString(10);   // "12"	 (base 10)
x.toString(16);   // "c"   (hex)
x.toString(8);    // "14"	 (octal)
x.toString(2);    // "1100"(binary)
```

Functionality to help with the display of numbers is offered in the _Numeral.js_ library.

&nbsp;


#### Constants

```js
Math.E        // ~2.718
Math.PI       // ~3.142

Math.LN2      // natural log of 2      : ~0.693
Math.LN10     // natural log of 10     : ~2.303
Math.LOG2E    // base 2 log of Math.E  : ~1.433
Math.LOG10E   // base 10 log of Math.E : ~0.434

Math.SQRT1_2  // sqrt of 1/2: ~0.707
Math.SQRT2    // sqrt of 2  : ~1.414
```

&nbsp;


#### Algebraic Functions 

```js
Math.pow(x, y)          // x^y
Math.sqrt(x)            // square root
Math.cbrt(x)            // cube root
Math.exp(x)             // e^x, eq to:    Math.pow(Math.E, x)
Math.expm1(x)           // e^x - 1 eq to: Math.pow(Math.E, x) - 1
Math.hypot(x1, x2,...)  // sqrt of arguments
```
&nbsp;


#### Logarithmic Functions

```js

Math.log(x)   // natural log of x
Math.log10(x) // base 10 log of x, eq to     : Math.log(x) / Math.log(10)
Math.log2(x)  // base 2 log of x, eq to      : Math.log(x) / Math.log(2)
Math.log1p(x) // natural log of 1 + x, eq to : Math.log( 1 + x )
```
&nbsp;


#### Misc

```js
Math.abs(x)          // absolute value of x
Math.sign(x)         // sign of x (if negative : -1, if positive: 1, if 0: 0)
Math.ceil(x)         // ceiling of x
Math.floor(x)        // floor of x
Math.trunc(x)        // integral art of x (all fractional digits removed)
Math.round(x)        // round to nearest int
Math.min(x1, x2,...) // min argument
Math.max(x1, x2,///) // max argument
```
&nbsp;


#### Psedorandom Number Generator

```js
Math.random()                         // range [0, 1)
x + (y-x)*Math.random()               // range [x, y)
m + Math.floor((n-m)*Math.random())   // Int in [m, n)
m + Math.floor((n-m+1)*Math.random()) // Int in [m, n]
```

One problem with it is that it cannot be seeded. If you need that, you can use the _seedrandom.js_ package.

&nbsp;

#### Trigonometric Functions


```js
Math.sin(x)			// sine of x
Math.cos(x)			// cosine of x
Math.tan(x)			// tangent of x
Math.asin(x)		// inverse sine of x    (arcsin)
Math.acos(x)		// inverse cosine of x  (arccos)
Math.atan(x)		// inverse tangent of x (arctan)
Math.atan2(y, x0)	// counterclockwise angle from x-axis to the point (x, y)
```
&nbsp;


#### Hyperbolic Functions

```js
Math.sinh(x)		// hyperbolic sine of x
Math.cosh(x)		// hyperbolic cos of x
Math.tanh(x)		// hyperbolic tan of x
Math.asinh(x)		// inverse hyperbolic sine    (arcsinh)
Math.acosh(x)		// inverse hyperbolic cosing  (arccosh)
Math.atanh(x)		// inverse hyperbolic tangent (arctanh)
```


