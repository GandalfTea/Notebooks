

### JS in the Browser

&nbsp;

&nbsp;


#### The Document Object Model

The DOM is a convention for describing the structure of a HTML document. It is a tree rooted in the _document_. 


<img src="https://i.imgur.com/9p4D8Z9.png" alt="img" style="zoom: 10%;" />


Code:

```html
<!doctype html>
<html>
	<head>
		<meta charset = 'utf-8'>
		<title>Simple HTML</title>
		<style>
			.callout {
				border: solid 1px #ff0080;
				margin: 2px 4px;
				padding: 2px 6px;
			}
			.code {
				background: #ccc;
				margin: 1px 2px;
				padding: 1px 4px;
				font-family: monospace;
			}	
		</style>
	</head>
	<body>
		<header>
			<h1>Simple HTML</h1>	
		</header>
		<div id = 'contents'>
			<p>This is a <i>simple</i>HTML file.</p>
			<div class = 'callout'>
				<p>This is as fancy as we'll get</p>
			</div>
			<p>IDs (such as <span class = 'code'>#content</span>)
				are unique.</p>
			<p>Classes (such as <span class = 'code'>.callout</span>)
				can be used on many elemnents</p>
			<div id='callout2' class='callout fancy'>
				<p>A simple HTML element can have multiple classes.</p>
			</div>
		</div>
	</body>
<html>
```

Every node has the properties `nodeType`, an integer relaying type and `nodeName`, keeping the name.    

The _Node_ object contains constants that map to these numbers. The primary node we will be dealing with is the _Node.ELEMENT_NODE_ (html elements) and _Node.TEXT_NODE_.


To print out the entire DOM to the console, you can do:

```js
function printDOM(node, prefix) {
	console.log(prefix + node.nodeName);
	for(let i = 0; i < node.childNodes.length; i++) {
		printDOM(node.childNodes[i], prefix + '\t');
	}
}
```
```js
printDOM(document, '');
```


&nbsp;

&nbsp;


#### DOM _get_ methods

```js
document.getElementById(' ... ');			
document.getElementByClassName(' ...');				// returns a collection of elements

const paragraph = document.getElementsByTagName(' ... ');	// returns a collection of elements
```

The collections returnd are not arrays, but _HTMLCollection_, an array-like object that does not contain methods like _map_, _filter_ and _reduce_. 

You can convert this object into an array by using the spread operator: `[...document.getElementByTagName(p)]`.

&nbsp;

&nbsp;



#### Querying DOM Elements


There is a much more general method that can locate elements by the relationship to other elements.

These methods are `querySelector` and `querySelectorAll` and they allow you to use CSS selectors.

CSS selectors allow you to identify elements by their type (`<p>`, `<div>`, etc.), their ID's and classes, or any combination of the bunch. You also have to use CSS syntax, therefore `<a>` becomes _a_, classes are `.class` and ID's with `#id`.

For example:
```js
document.querySelectorAll('div')
```
Prints out all the `div`'s.

You can also specify descendents. For example `#content p` will select `<p>` elements that are descendents in the tree of whatever element has the `#content` tag.

You can also specify direct children by using `>` like : `#content > p`. This will affect the `<p>` elements that are children to elements with the tag.

&nbsp;

&nbsp;

#### Manipulation DOM Elements


Each element has two properties, `textContent` that strips out all HTML tahs and provides text data only, and `innerHTML` that allows you to create HTML:


```js
const para1 = document.getElementByNameTag('p')[0];

para1.textContent;					// "This is a simple HTML file"
para1.innerHTML;					// "This is a <i>simple</i> HTML file"
para1.textContent = "Modifies HTML file";
para1.innerHTML = "<i>Modified</i> HTML file";
```

&nbsp;

&nbsp;

#### Creating new DOM Elements

```js
const p1 = document.createElement('p');
const p2 = document.createElement('p');

p1.texContent = "I was created in JS";
p2.textContent = "Sure";
```


To add these to the DOM, you hae to use `insertBefore` and `appendChild` and get references to the parent elements:

```js
const parent = document.getElementById('content');
const firstChild = parent.childNodes[0];
```
```js
parent.insertBefore(p1, firstChild);
parent.appendChild(p2);
```

&nbsp;

&nbsp;


#### Styling Elements


With DOM API, you have complete control over styling, but it is good to keep all this in the CSS file.


As an example, if you wanted to highlight all paragraphs that contain the word unique, you would first declare in CSS:

```css
.highlight {
	background: #ff0;
	font-style: italic;
}
```

Then you find all the `<p>` tags, check for the word and add the highlight class:

```js
function highlightParas(containing) {

	if(typeof containing === 'string')
		containing = new RegExp(`\\b${containing}\\b`, 'i');

	const paras = document.getElementByTagName('p');
	
	for(let p of paras) {
		if(!containing.text(p.textContent)) continue;
		p.classList.add('highlight');
	}
}
```
```js
highlightParas('unique');
```


If we wanted to remove the highlight:

```js
function removeParamHighlight() {
	const paras = document.querySelectorAll('p.highlight');
	for(let p of paras) {
		p.classList.remove('highlight');
	}
}
```
&nbsp;

&nbsp;

#### Data Attributes


HTML5 introduced _data attributes_ which allow you to add data that isn't rendered by the browser but easily read by JS.

Let's add two buttons, one for adding the highlight and one for removing it:

```html
<button data-action="highlight" data-containing="unique">
	Highlight paragraphs containing "unique"
</button>

<button data-action="removeHighlights">
	Remove highlights
</button>  
```

You can name the data properties however you want, we chose _action_ and _contains_.

Get all the elements with those properties:

```js
const highlightActions = document.querySelectorAll('[data-action="highlight"]');
```

If we look at one of the elements, you can see it has a `dataset` property:

```js
highlightActions[0].dataset;	// DOMStringMap { containing: "unique", action: "highlight" }
```

We can also add data attributes with JS:

```js
highlightActions[0].dataset.containing = "giraffe"
highlightActions[0].dataset.caseSensitive = "true";
```

&nbsp;

&nbsp;

#### Events

The DOM API describes almost 200 events. On top of this, each browser implements nonstandars events of it's own.

One of the main ones is the `click` event:

```js
const highlightActions = document.querySelectorAll('[data-action="highlight"]');
for(let a of highlightActions) {
	a.addEventListener('click', evt => {
		evt.preventDefault();
		highlightParas(a.dataset.containing);
	});
}
```
```
const removeHighlightActions = document.querySelectorAll('[data-action="removeHighlights"]');

for(let a of removeHighlightActions) {
	a.addEventListener('click', evt => {
		evt.precentDefault();
		removeParaHighlights();
	});
}
```

`addEventListener` allows you to specify a function that will be called when a specific event occurs. That function takes a single _Event_ argument. Depending on the event, this object might have different properties. For example, the _click_ event has `clientX` and `clientY`, describing the coordinates of the click, as well as a `target` which is the element clicked. 

Many different elements have default behaviour, like `<a>` opens the link. To prevent the default methods, you call `preventDefault()` on the _Event_ object. i


&nbsp;

&nbsp;

#### Event Capturing and Bubbling


Because the structure of HTML is hierarchical, there are more than one elements that could potentially respond to an event. How is it decided who responds first? 

There are two options. One starts with the most distant ancestor, called _capturing_. The other option is to start where the event occured and then go up, called _bubbling_. To support both, HTML5 allows handlers to capture the event (start from top) and then the event bubbles back up from the target element.


To stop this from happening, you can call `stopPropagation()`. This prevents all propagation past the initial object. `stopImmediatePropagation` will prevent any further handler from getting called (even on the current element). To see this in action:


```html
<!doctype html>
<html>
	<head>
		<title>Event Propagation</title>
		<meta charset='utf-8'>
	</head>
	<body>
		<div>
			<button>Click Me</button>
		</div>
		<script>
			
			// create an event handler and return it
			function logEvent(handlerName, type, cancel, stop, stopImmediate) {
				// actual event handler
				return function(evt) {
					if(cancel) evt.preventDefault();
					if(stop) evt.stopPropagation();
					if(stopImmediate) evt.stopImmediatePropagation();
					console.log(`${type}: ${handlerName}` +
						(evt.defaultPrevented ? ' (canceled)' : ''));
				}
			}

			// add an event logger to an element
			function addEventLogger(elt, type, action) {
				const capture = type === 'capture';
				elt.addEventListener('click',
					logEvent(elt.tagName, type, action==='cancel',
					action==='stop', action==='stop!'), capture);
			}

			const body = document.querySelector('body');
			const div = document.querySelector('div');
			const button = document.querySelector('button');

			addEventListener(body, 'capture');
			addEventListener(body, 'bubble');
			addEventListener(div, 'capture');
			addEventListener(div, 'bubble');
			addEventListener(button, 'capture');
			addEventListener(button, 'bubble');
		</script>
	</body>
</html>
```


When clicking on the button, the console print:

```
capture: BODY
capture: DIV
capture: BUTTON
bubble: BUTTON
bubble: DIV
bubble: BODY
```

If we cancel propagation:

```
capture: BODY
capture: DIV (canceled)
capture: BUTTON (canceled)
bubble: BUTTON (canceled)
bubble: DIV (canceled)
bubble: BODY (canceled)
```

If we stop propagation at the `<button>` capture:

```
capture: BODY
capture: DIV (canceled)
capture: BUTTON (canceled)
bubble: BUTTON (canceled)
```

And if we stop immediately on the button capture:

```
capture: BODY
capture: DIV (canceled)
capture: BUTTON (canceled)
```

&nbsp;

&nbsp;

#### Event Categories

[MDN](https://developer.mozilla.org/en-US/docs/Web/Events#Categories) has an excelent reference for all DOM events grouped into categories.


Some of the main ones are:

_Drag Events_: Allow the implementation of drag-and-drop interface with events like `dragstart`, `drag`, `dragend`,`drop`, etc.

_Focus Events_: Allows the user to interact with editable components, such as field forms. `focus` is raised when a user enters the field with a clock, Tab, or touch. `blur` is raised when the user clicks away. The `change` event is raised when the user makes a change in the field.

_Form events_: When a user submits a form (by pressing _Submit_), the `submit` event is raised on the form.


_Input device events_: Mouse events: `click`, `mousedown`, `move`, `mouseup`, `mouseenter`, `mouseleave`, `mouseover`, `mousewheel`. And keyboard: `keydown`, `keypress`, `keyup`. Note that touch events take precedence over mouse events.

_Media Events_: Allows you to track a user's interaction with HTML5 video and audio players.

_Progress events_: Info about the browser load content. Most common is `load` which fires once the browser has loaded the element and all dependent resources. `error` is also useful, allowing you to take action when an element is unavailable.

_Touch events_: Mutiple simpultanious touches are permitted.


&nbsp;

&nbsp;

#### Ajax

Ajax is short for _Asynchronous JavaScript and XML_, it allows you to have an async connection to a server, updating elements without refreshing the page. This was made possible with the introduction of the _XMLHttpRequest_ object and ushered what became known as Web 2.0.


To test Ajax, we'll build a simple server in Node.js that exposes an Ajax _endpoing_:

```js
const http = require('http');

const server = http.createServer(function(req, res) {
	res.setHeader('Content-Type', 'application/json');
	res.setHeader('Access-Control-Allow-Origin', '*');
	res.end(JSON.stringify({
		platform: process.platform,
		nodeVersion: process.version,
		uptime: Math.round(process.uptime())
	}));
});

const port = 7070;
server.listen(port, function() {
	console.log('Ajax server started on port ${port}');
});
```


Note: Ajax introduces the possibility of a vulnerability called _cross-origin_. We use `Access-Contol-Allow-Origin` with the value of `*`, which signals to the cliend not to prevent the call for security reasons. On a production server, you would either want to use the same protocol, domain and port, or specify explicitly what protocol, domain and port can access the endpoint.

To start the server:

```
$ babel-node ajaxServer.js
```

You can access it at: _http://localhost:70070_ in your browser. 


In the HTML page, add a placeholder that recieves the info:

```html
<div class='serverInfo'>
	Server is running on <span data-replace='platform'>???</span>
	with Node <span data-replace='nodeVersion'>???</span>. It has
	been up for <span data-replace='uptime'>???</span> secondsd.
</div>
```


Now we can perform the Ajax call:

```html
<script type='application/javascript;version=1.8'>
	function refreshServerInfo() {
		const req = new XMLHttpRequest();
		req.addEventListener('load', function(){
			// TODO: put those values into HTML
			console.log(this.responseText);
		})
		req.open('GET', 'http://localhost"7070', true);
		req.send();
	}
	refreshServerInfo();
</script>
```

Now we just have to insert this data into out HTML. We iterate through the object from the server `Object.keys` and look for the `replace` attrubute:


```js
req.addEventListener('load', function() {
	// this.responseText is a JSON string, we use Json.parse to convert to object
	const data = JSON.parse(this.responseText);

	// we only want to replace text within the servInfo div
	const serverInfo = document.querySelector('.serverInfo');

	// iterate over the keys
	Object.keys(data).forEach(p => {
		
		// find elements to replace
		const replacements = server.quearySelectorAll('[data-replace="${p}"]');
		for(let r of replacements) {
			r.textContents = data[p];	
		}
	});
});
```


Now we just have to call the `refreshServerInfo` function whenever we want. To do so periodically:
```js
setInterval(refreshServerInfo, 200);
```

Note: On a slow internet connection, the user may see the placeholder `???`. This is called the _flash of unstyled content_ problem. One solution is to load the page with the correct values.
