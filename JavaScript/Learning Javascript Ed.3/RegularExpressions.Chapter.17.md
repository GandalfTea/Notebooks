

### Regular Expressions 

&nbsp;


It is regularely abbreviated to _regex_ or _regexp_.          

If any regex literals look confusing, you can use this site for a detailed breakedown : https://regexr.com/       
Just copy and paste it there, along with the example text.

&nbsp;

&nbsp;


#### Substring Matching and Replacing 


This is the functionality available in JS without the use of regex:

```js
const input = 'As I was going to Saint Ives';
```
```js
input.startsWith('As');			// True
input.endsWith('Ives');			// True
input.startsWith('going', 9);		// True, starts at index 9
input.endsWith('going', 14);		// True, treat index 14 as end of the string.
input.includes('going');		// True
input.includes('going', 10);		// False, start at index 10
input.indexOf('going');			// 9
input.indexOf('going', 10);		// -1
input.indexOf('nope');			// -1
```

Note that all of the methods are case sensitive. 
You can transform them with:

```js
input.toLowerCase().startsWith('as');	// True
```

You can also replace:

```js
const output = input.replace("going", "walking");
```

The input string is not modified, a new string is created. 

&nbsp;

&nbsp;


#### Constructing Regular Expressions 

Regexes can be literals or you can make a dedicated RegExp object.          

We will be addressing literal regexes as _regex_ and objects as _RegExp_.      

Literals are inclosed between slashes : `/ . . . /`.     

RegExp objects are represented by the class _RegExp_:

```js
const re1 = /going/;				// this is how you search for 'going' in literal
const re2 = new RegExp("going");		// equivalent object constructor
```
&nbsp;

&nbsp;

#### Searching


We'll be using the regex `/\w{3,}/ig` for the search, but it will be discussed later. It matches all words with three letters or longer.

```js
const input = "As I was goinh to Saint Ives";
const re = /\w{3,}/ig;
```

```js
re.test(input);		// True (input has at least one word with three letters)
re.exec(input);		// ["was"] (first match)
re.exec(input);		// ["going"]  (exec remembers where it was)
```
This can also be done with the regex inline:

```js
input.match(/\w{3,}/ig);
input.search(/\w{3,}/ig);
/\w{3,}/ig.test(input);
/\w{3,}/ig.exec(input);
```
&nbsp;

&nbsp;



#### Replacing

The same `String.prototype.replace` method also accepts regex. 

```js
const input = "As I was going to Saint Ives";

const output = input.replace(/\w{4,}/ig, '****');	// As I was **** to **** ****
```


&nbsp;

&nbsp;

#### Input Consumption

Regex is a pattern for consuming input strings.    

Propertires of regex algorithnm:

* Strings are consumed from left to right one character at a time.
* Once a character is consumed, it is never revisited.
* If there is no match, the regex advances one character attempting to find a match.
* If there is a match, all the characters are consumed at once. Matching continues with the next character. It does not detect overlapped words.

&nbsp;

&nbsp;


#### Alteration


If you have to match HTML tags in a string, some of which could be mixed case, alterations are helpful:

```js
const html = 'HTML with <a href="/one">one link</a>, and some JavaScript.' +
	'<script src="stuff.js"></script>';

const matches = html.match(/area|a|link|script|source/ig); 	// [ "a", "link", "a" . . .]
```

Alteration is signaled by the `|` character. Notice we put the `area` tag before `a` because if the `a` is consumed first, there will be no match for `area`.   

The `ig` signifies to _ignore case_ (i), and _search globally_ (g).
Without the `g`, only the first match will be returned. 

&nbsp;

&nbsp;


#### Matching


Regular expressions work on regular languages only. Using it on a ore complex language might fail. To have a solution that works all the time, you need a parser. Consider :

```js
const html = '<br> !CDATA[[<br>]]';
const matches = html.match(/<br>/ig);
```

The regex will match twice, but there is only one true `<br>` in out example. 


&nbsp;

&nbsp;

#### Character Sets

If you wanted to find all numbers in a string, you could do:
```js
const beer99 = "99 bottles of beer on the wall" +
	"take 1 down and pass it around --" +
	"98 bottles of beer on the wall.";

const matches = beer99.match(/0|1|2|3|4|5|6|7|8|9/g);
```

But that is stupid and shitty, so you can instead do:


```js
const m1 = beer99.match(/[0123456789]/g);	// still kinda shitty
const m2 = beer99.match(/[0-9]/g);		// much better
```
You can even combine ranges:

```js
const match = beer99.match(/[\-0-9a-z.]/ig);   // also works :  /[.a-z0-9\-]/
```

You can also negate character sets using `^`:

```js
const match = beer99.match(/[^\-0-9a-z.]/);
```

&nbsp;

&nbsp;

#### Named Character Sets

Some character sets are so common that they have been named for ease of use:

```
* [0-9]			--  \d
* [^0-9]		--  \D
* [ \t\v\n\r]		--  \s	(tabs, cpaces amd vertical tabs)
* [^\t\v\b\r]		--  \S
* [a-zA-Z_]		--  \w	(no dashes or periods)
* [^a-zA-Z_]		--  \W
```

```js
const stuff =
	'high:		9\n' +
	'medium:	5\n' +
	'low:		2\n';
```
```js
const levels = stuff.match(/:\s*[0-9]/g);
```
The `*` says _zero or more whitespaces_.


```js
const messyPhone = '(505) 555-1515';
const neatPhone  = messyPhone.replace(/\D/g, '');
```
```js
const field = '		something		';
const valid = /\S/.text(field);
```

&nbsp;

&nbsp;

#### Repetition


Using our previus beer example, if we wanted to match numbers with more digits, we might do something like:

```js
const match = beer99.match(/[0-9][0-9][0-9] | [0-9][0-9] | [0-9]/);
```

Notice also that we have to put the longer numbers (3 digit) in the front because smaller numbers might consume parts of bigger ones.

There is a better way to search for multiple character numbers:

```js
const match = beer99.match(/[0-9]+/);
```

The `+` character means that the _preceding element_ should match _one or more times_.

Out side of this one, there are 5 repetition modifiers:

```
{n}		--		exactly _n_				/d{5}/
{n,}		--		at least _n_				/\d{5,}/
{n, m}		--		at least _n_, at most _m_		/\d{2, 5}/
?		--		{0, 1}					/[a-z]\d?/i
*		--		zero or more (Klene star)		/[a-z]\d*/i
+ 		--		one or more				/[a-z]\d+/i
```


&nbsp;

&nbsp;

#### The Period Metacharacter and Escaping

Period `.` means _match anything_ except new lines. It is very often used to consume things that are unimportant. 

```js
const input = 'Address: 333 Main St., Anywhere, NY, 55532. Phone: 555-555-2525.';

const match = input.match(/\d{5}.*/);
```

You might want to match a period literal or things that are regex metacharacters. To escape any special regex caracter, simply prefix it with a backslash:

```js
const equation = "(2 + 3.5) * 7";

const match = equation.match(/\(\d \+ \d\.\d\) \* \d/);

//  /  ( \d  \+  \d\.\d\ )  \*   \d   /
```

&nbsp;

&nbsp;


##### A True Wildcard


The most common way to match everything, including new lines, is `[\s\S]`. This matches everything that is whitespace, and everything that is not whitespace, meaning everthing. 

&nbsp;

&nbsp;


#### Grouping


So far we have searched for single characters. Grouping allows us to search for _subexpressions_. 

Groups look like this : `(?:<subexpression>)`.

If you were matching domain names, but only wanted the ones with _.com_ and _.org_ and _.edu_:

```js
const text = "Visit example.com today!";
const match = text.match(/[a-z]+(?:\.com|\.org|\.edu)/i);
```

Grouping allows you to apply repetition to the whole string. 
If you want to match URLs and want to include the ones that start with _http://_, _https://_ or _//_ (protocol independent), you can use a group with a zero-or-one `?` repetition:

```js
const html = '<link rel="stylesheet" href="http://insecure.com/stuff.css">\n' +
'<link rel="stylesheet" href="https://secure.com/securestuff.css">\n' +
'<link rel="stylesheet" href="//anything.com/flexible.css">';

const matches = html.match(/(?:https?)?/\/\[a-z][a-z0-9-]+[a-z0-9]+/ig);
```

Brakedown of this example:     

`(?:https?)?` There are two zero-or one repetitions here. The fist one says the `s` is optional (Remember that repetition characters refer only to the character to their immediate left.), the second one refers to the while group to its left. Taken all together, this wil match the empty string, _http_, or _https_.       

`/\/\` We match and escape two slashes.        

`[a-z][a-z0-9-]+[a-z0-9]+`	This encapsulates almost all the characters that could be in a URL. Domain names might have numbers, letters or dashes.       

The example isn't perfect. It would match _//gotcha_ just like _//valid.com_. 

&nbsp;

&nbsp;

#### Lazy Matches, Greedy Matches


Regular expressions, by default, are _greedy_. Meaning they will match as much as possible before stoping.

Consider you have some HTML you want to replace, for example turn `<i>` into `<strong>`:

```js
const input = "Regex pros know the difference between\n"  +
	"<i>greedy</i> and <i>lazy</i> matcing.";

input.raplace(/<i>(.*)<\/i>/ig, '<strong>$1</strong>');
```
The `$1` is the replacement string that will be replaced by the content of the group `(.*)` in the regex.      

If you try it, the result will be :

```
"Regex pros know the difference between
<strong>greedy</i> and <i>lazy</strong> matching".
```

Because it is greedy, after it sees the `<i>`, it will not stop until it discovers the last `</i>`.      

We can solve this by making the repetition character `*` lazy:

```js
input.replace(/<i>(.*?)<\/i>/ig, '<strong>$1</strong>');
```

The only difference is the question mark `?` inside the `(.*?)`. Now it will stop as soon as it sees a `<i>`.     


All the repetition characters: `*`, `+`, `?`, `{n}`, `{n,}` and `{n, m}` can be followed by a `?` to make them lazy.    

&nbsp;

&nbsp;

#### Backreferences


Every group in a regex is assignes a number, from left to right, starting with 1. You can refer to that group witha backslash followed by the number.    
As an example, `\1` means _whatever group number 1 matched_.

Using single or double quotes in HTML enables us to do:

```js
const html = `<img alt='A "simple" example.'> ` +
		`<img alt="Don't abuse it!">`;

const match = html.match(/ <img alt= (?:['"]) .*? \1 /g>);
```

The first group will match either single or double quotes, followed by a zero or more characters (note this is lazy), followed by `\1` refering to whatever the first match was, either single or double quote. 


&nbsp;

&nbsp;


#### Replacing Groups


If you want, for example, to replace everything in the string but the `href` and `<a>` tags:

```js
let html = '<a class = "nope" href="/yep">Yep</a>';

html = html.replace(/<a .*?(href=".*?").*?>/, '<a $1>');
```
In the literal, we refer to _whatever group one catched_ as `\1` but, in the replacement string it becomes `$1`.          
In addition, we also have `$'`(tick) for _everything before the match_, `$&` for _the match itself_ and `$'`(apostrophe) for _everything after the match_.           

For the `href` and `class` attribute:
```js
let html = '<a class = "nope" href="/yep" id="nope">Yep</a>';

html = html.replace(/<a .*?(class=".*?").*?(href=".*?").*?>/, '<a $2 $1>');
```

&nbsp;

&nbsp;

#### Function Replacement

Following the prev. example, changing all `<a>`'s into something else and keep `class`, `id`, and `href`. This time, the input is messy.

```js
const html = 
	`<a class="foo" href="/foo" id="foo">Foo</a>\n` +
	`<A href='/foo' Class="foo">Foo</a>\n` +
	`<a href="/foo">Foo</a>\n` +
	`<a onclick="javascript:alert('foo!')" href="/foo">Foo</a>`;
```

We can deal with the big number of variation by using two regexes: one to recognize `<a>` tags, another to replace the contants of the tag with only what we want.

Concerning the second regex, we can not guarantee that the attributes are in order. We can solve this by using the _String.prototype.split_ method to consider one attrubute at a time:

```js

function sanitizeATag(aTag) {

	// get the parts of the tag
	const parts = aTag.match( /<a \s+ (.*?)> (.*?) <\/a> /i );
	
	// parts[1] are attributes of the opening <a> tag
	// parts[2] are what's between the <a> and </a>
	
	const attributes = parts[1]
		//split it into individual attributes
		.split(/\s+/);
		
	return '<a ' + attributes
		.filter(attr => /^(?:class|id|href)[\s=]/1.test(attr))
		// joined by spaces
		.join(' ')
		+ '>'
		// add the contents
		+ parts[2]
		+ '</a>';
}
```
Getting the `<a>` tags:
```js
html.match(/<a .*?>(.*?)<\/a>/ig);
```
```js
html.replace(/<a .*?>(.*?)<\/a>/ig, function(m, g1, offset) {
	console.log('<a> tag found at ${offset}. contents: ${g1}');
});
```

The arguments _String.prototype.replace_ recieves are:

* The entire matched string (eq to `$&`)
* The matched group. There will be as many of these arguments as there are groups.
* The offset of the match within the original string (number)
* The original string.

Using our function:

```js
html.replace(/<a .*?<\/a>/ig, function(m) {
	return sanitizeATag(m);
});
```

Or, the CHAD way:

```js
html.replace(/<a .*?<\/a>/ig, sanitizeATag);
```

&nbsp;

&nbsp;


#### Anchoring

There are two anchors, `^` that matches at the beginning of the line and `$` that matches at the end of the line.

```js
const input = "It was the best of all times, it was the worst of all times";
const beginning = input.match(/^\w+/g);		// "It"
const end = input.match(/\w+$/g);		// "times"
const everything = input.match(/^.*$/g);	// same as input
const nomatch1 = input.match(/^best/ig);
const nomatch2 = input.match(/worst&/ig);
```

They match the beginning and end of the whole string, even if you have newlines in it.    
If you want to treat the string as multiline, you have to use the `m` option:

```js
const input = "One line\nTwo lines\nThree lines\nFour";

const beginnings = inpit.match(/^\w+/mg);		// ["One", "Two", "Three", "Four"]
const endings = input.match(/\w+$/mg);			// ["line", "lines", "lines", "Four"]
```

&nbsp;

&nbsp;

#### Word Boundary Matching

The word boundary metacharacters are `\b` and the inverse, `\B`. They do not consume input.    

They are defined where a `\w` match is either preceded by or followed by a `\W` nonword character, or the beginning and end of a string. 


```js
const inputs = [
	"john@doe.com", 		// nothing but the email
	"john@doe.com is my email", 	// email at the beginning
	"my email is john@doe.com",	// email at the end
	"use john@doe.com, my email", 	// email in the middle, with comma afterward
	"my email:john@doe.com." 	// email surrounded with punctuation
];
```

```js 
const emailMatcher =
	/\b[a-z][a-z0-9._-]*@[a-z][a-z0-9_-]+\.[a-z]+(?:\.[a-z]+)\b/ig;
```
```js
inputs.map(s => s,replace(emailMatcher, '<a href="mailto:$&">$&</a>'));

// returns [
// 		"<a href="mailto:john@doe.com">john@doe.com</a>",
// 		"<a href="mailto:john@doe.com">john@doe.com</a> is my email",
// 		"my email is <a href="mailto:john@doe.com">john@doe.com</a>",
// 		"use <a href="mailto:john@doe.com">john@doe.com</a>, my email",
// 		"my email:<a href="mailto:john@doe.com>john@doe.com</a>.",
// ]
```

&nbsp;

&nbsp;

#### Lookaheads


They don't consume input and you can match any subexpression. Basically, they are general purpose.     
They are necesarry when there is overlapping content.


Validating a password matching a policy:

```js
function validPassword(p) {
	return /[A-Z]/.test(p) &&		// at least one uppercase letter
		/[0-9]/.test(p) &&		// at least one number
		/[a-z]/.test(p) &&		// at least one lowercase letters
		!/[^a-zA-Z0-9]/.test(p);	// only letters and numbers
}
```
If we wanted to combine all that into one expression, we would do something like this:

```js
function validatePassword(p) {
	return /[A-Z].*[0-9][a-z]/.test(p);
}
```

We haven't tested for invalid characters. Oh no

This is where we use lookaheads, being the only independent regex that does not consume the input.    
They look like this : `(?=< . . .>)`. You can also have negative lookaheads : `(?!< . . . >)`

Using this, we can write a single regex to validate the password:

```js
function validPassword(p) {
	return /(?=.*[A-Z])(?=.*[0-9])(?=.*[a-z])(?!.*a-zA-Z0-9)/.test(p);
}
```
&nbsp;

&nbsp;

#### Constructing Regexes Dynamically


One case where we need to use the RegExp object is when we want to construct them _dynamically_.

You might have an array of usernames you want to match into a string:

```js
const users = ["mary", "nick", "arghur", "sam", "yvette"];
const text = "User @arthur started the backup at 15:15, " +
	"and @nick and @yvette restores it at 18:35.";

const userRegex = new RegExp(`@(?:${users.join('|')})\\b`, 'g');
text.match(userRegex);		// [ "@arthur", "@nick", "@yvette" ]
```

Note that we have to use backslashed before the `b` (word boundry metachar.) used to escape the one in the string.


