

### jQuery

It is a popular library for manipulating the DOM and executing Ajax requests. It does nothing that the original DOM API can't do, but it offers some advantages:

* it protects you from the different ways browsers implement the DOM API.
* it provides a simple Ajax API.
* it offers enhancements on the built-in DOM API



#### The Dollar Sign

When you include jQuery in your code, you can use the variable `jQuery` or just `$`.

You include it by using a CDN:

```html
<script src="//code.jquery.com/jquery-2.1.4.min.js"></script>
```

jQuery allows you to put your code in a callback that will run once the browser has fully loaded:

```js
$(document).ready(function() {
	// . . .
})
```

It is safe to use multiple times. There is also a short version:

```js
&(function() {
	// . . .
})
```

It is common to put all your code inside such a block.




#### jQuery-Wrapped DOM Elements


You first have to create an object that is wrapped around the set of DOM objects:

```js
const $paras = $('p');		// object matching <p>
$paras.length;				// number of paragraph tags matched
typeof $param;				// "object"
$paras instanceof $;		// True
$paras instanceof jQueey;	// True
```


Calling jQuery with HTML creates new DOM elements:

```js
const $newPara = $('<p>Newly creates paragraph...</p>');
```

A good convention is to name variables that are created with jQuery starting with _$_. 


#### Manipulating Elements


It offers text and html methods roughly equivalent to JS.

To replace all paragraphs with the same text:

```js
$('p').text('ALL PAHARGRAPHS REPLACED');
```
Or edit html:
```js
$('p').html('<i>ALL</i> PARAGRAPHS REPLACED');
```


If you wanted to modify only one paragraph, you have to specify the index:


```js
$('p').eq(2)	// third paragraph
	.html('<i>Third</i> PARAGRAPH REPLACED');
```

All methods return a iQuery object, allowing you to chain calls.

To remove all elements:
```js
$('p').remove();
```


One of the ways of adding new content (among many) is:

```js
$('p').append('<sup>*</sup>');
``` 

We can also insert siblings _before_ or _after_:

```js
$('p').after('<hr>')
	.before('<hr>');
```

There are also methods that reverse the order of insertion:

```js

$('<sup>*</sup>').appendTo('p');		// eq: $('p').append( . . . )
$('<hr>').appendBefore('p');			// eq: $('p').before( . . . )
$('<hr>').appendAfter('p');				// eq: $('p').after  . . . )
```

You can add or remove classes, or _toggle_ classes (add if it doesn't have it, remove if it does)
You can also modify style directly with css:

```js
$('p:odd').css('color', 'red');
```


To filter the elements we modify, we can also use `filter`, `not` and `find`:

```js
$('p').after('<hr>')
	.append('<sup>*</sup>')
	.filter(':odd')
	.css('color', 'red');
```

`not` is the inverse of `filter`. We filter the params we do not want:

```js
$('p').after('<hr>')
	.not('.highlight')
	.css('margin-left', '20px');
```

`find` returns a set of _descendants_ that match a certain query:

```js 
$('p').before('<hr>')
	.find('.code')
	.css('font-size', '30px');
```



#### Unwrapping jQuery Objects


We can get access to the underlying DOM methods by using `get`:
```js
const para2 = $('p').get(1);		// second <p>
const paras = $('p').get();			// all <p> elements
```




#### Ajax


jQuery exposes an `ajax` method that allows sophisticated control over an Ajax call:


```js
function refeshServer() {
	const $serverInfo = $('.serverInfo');
	$.get('http://localhost:7070').then(
		// success
		function(data) { 
			Object.keys(data).forEach(p => {
				$('[data-replace="${p}"]').text(data[p]);
			});
		},
		function(jqXHR, textStatus, err) {
			console.error(err);
			$serverInfo.addClass('error')
				.html('Error connecting to server.');
		}
	);
}
```

The methods `get` and `post` perform the most common types of Ajax calls. While those methods support comebacks, they return promises, which is the recommanded way to handle the server response.

