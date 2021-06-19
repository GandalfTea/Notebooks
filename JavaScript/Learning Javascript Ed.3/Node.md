

### Node


&nbsp;

&nbsp;


#### Modules 

_Modules_ are the mechanism for packaging and _namespacing_ code. 

If you wanted to create a module and use it in another file, you can do it like this:

&nbsp;

_file.js_
```js
module.exports = {
	geometricSum(a, x, n) {
		if(x === 1) return a*n;
		return a*(1 - Math.pow(x, n))/(1 - x);
	},
	arithmeticSum(n) {
		return (n + 1)*n/2;
	},
	quadraticFormula(a, b, c) {
		const D = Math.sqrt(b*b - 4*a*c);
		return [(-b + D)/(2*a), (-b - D)/(2*a)];
	},
};
```
```
const MathFunctions = require('file.js');

MathFunctions.geometricSum(1, 2, 5);
```

What you import is an object with all functions as methods. You can also do it like this:

```js 

exports.geometricSum(a, x, n) {
	if(x === 1) return a*n;
	return a*(1 - Math.pow(x, n))/(1 - x);
}

exports.arithmeticSum(n) {
	return (n + 1)*n/2;
}

exports.quadraticFormula(a, b, c) {
	const D = Math.sqrt(b*b - 4*a*c);
	return [(-b + D)/(2*a), (-b - D)/(2*a)];
}
```

&nbsp;

&nbsp;

#### Core Modules, File Modules, npm Modules


Module Types

TODO: Table




Core Modules:

TODO: Table


File moduals are the ones discused before.

_npm_ modules are file modules with a specific naming convention. If you require some module _x_, Node will look in the current directory for a subdirectory called _node_modules_. If it finds it, it will look for _x_ there. If it doesn't, it will go up to the parent directory and repete the process. So on until it reaches root. 

&nbsp;

&nbsp;


#### Customizing Modules with Function Modules


There is another pattern of exporting, exporting a function with the intent of running it immediately and using the output. 

A real world example is `debug` package:

```js
const debug = require('debug')('main');		// we imm call the function

debug('starting');				// will log: "main starting +0s"
```


Debug returns a function that returns another function that remembers the string fron the first function. If we wanted to write it outselves:

```js
let lastMessage;

module.exports = function(prefix) {
	return function(mesage) {
		const now = Date.now();
		const sinceLastMessage = now - (lastMessage || now);
		console.log(`${prefix} ${message} +${sinceLastMessage}ms`);
		lastMessage = now;
	}
}
```
This function should be called right away so that the value of `prefix` can be backed into the module. 


This begs the question, what happens when we import a module multipe times? For example :

```js
const debug1 = require('./debug')('one');
const debug2 = require('./debug')('two');

debug1('started first');
debug2('started second');

setTimeout(function() {
	debug1('after some time...');
	debug2('what happens?');
}, 200);
```

You might expect:
```
one started first debugger! +0ms
two started second debugger! +0ms
one after some time... +200ms
two what happens? +200ms
```
But you actually get:
```
one started first debugger! +0ms
two started second debugger! +0ms
one after some time... +200ms
two what happens? +0ms
```

Node only imports a module once, and uses the same instance, meaning that even though `debug1` and `debug2` are separate functions, they share the same `lastMessage`. 

&nbsp;

&nbsp;

#### Filesystem Access


To create a file, use `fs.writeFile`, this example is placed where the node file is.

```js
const fs =require('fs');

fs.writeFile('hello.txt', 'hello from the other side', function(err) {
	if(err) return console.error('Error writing the file.');
});
```
&nbsp;

Node records the source directory in a variable named `__dirname`. We could change out example to:

```js

const fs = require('fs');
const path = require('path');

fs.writeFile(path.join(__dirname, 'hello.txt'),
		'hello from the other side', function() {
	if(err) return console.error('Error');
});
```

Now the file will always be created in the source directory.

Using string concat is not always good for cross-platform compatibility. This can cause problems on Windows. A good way would be to use `path.join` like in the example. It will use directory separators depending on the systems.

&nbsp;


If we wanted to read the contents of the file:


```js
const fs = require('fs');
const path = require('path');

fs.readFile(path.join(__dirname, 'hello.txt'), function(err, data) {
	if(err) return console.errror('Error');
	console.log('Read:');
	console.log(data);
});
```

The result is unexpected:

```
Read:
<Buffer 68 65 6c 6c 6f 20 66 72 6f 6d 20 4e 6f 64 65 21>
```

If you do not tell `readFile` the desired encoding, it will return a buffer. The text is correct if you transform the hex to ASCII/Unicode. The default Unicode encoding is UTF-8, we can specify that we want this:


```js
fs.readFile(path.join(__dirname, 'hello.txt'),
		{ encoding: 'utf8' }, function(err, data) {
	. . .
});
```

&nbsp;

All of the functions in `fs` have a synchronous equivalent that does error handling with exceptions:


```js

try {
  //fs.reafFileSync( . . . )
	fs.writeFileSync(path.join(__dirname, 'hello.txt'), 'hello bigtch');
}catch(err) {
	console.error('Error');
}
```

&nbsp;


You can list all the files in a directory with `fs.readdir`:

```js
const fs = require('fs');
	
fs.readdir(__dirname, function(err, files) {
	if(err) return console.error('Error bitc');
	console.log('Contents of ${__dirname}:');
	console.log(files.map(f => '\t' + f).join('\n'));
});
```


More methods can be found in the [Node API Docs](https://nodejs.org/api/fs.html).

&nbsp;

&nbsp;


#### Process


Every Node programm has access to a variable named `process` that allows it to get info and control it's own execution. For example, if your app encounters a fatal error, you can stop it using `process.exit`. You can also provide an exit code. As another example, a program that reads _.txt_ files in a directory without any would exit with a code:

```js
const fs = require('fs');

fs.readdir('data', function(err, files) {
	if(err) {
		console.error('Fatal Error: can't read data');
		process.exit(1);
	}
	const txtFiles = files.filter( f => /\.txt$/i.text(f));
	if(txtFiles.length === 0) {
		console.log('No .txt files to process');
		process.exit(0);
	}
	// process .txt files here
});
```


This object gives you access to an array of _command-line arguments_ passed to the program. For example, giving gilenames as this type of argument:

```
$ node linecount.js file1.txt file2.txt, file3.txt
```


To access the array, you do:

```js
console.log(process.argv);
```

Along with the arguments given, there are a couple extra:

```js

[	'node',
	'/home/jdoe/linecount.js',
	'file1.txt',
	'file2.txt',
	'file3.txt'	  ]
```

First argument is the _interpreter_, then the path to the script executed, rest are passed arguments. 

We will use `array.slice` to get rid of the first ones:

```js
const fs = require('fs');

const filenames = process.argv.slice(2);

let counts = filenames.map(f => {
	try {
		const data = fs.readFilesSync(f, { encoding: 'utf8'});
		return `${f}: ${data.split('\n').length}`;
	}catch(err) {
		return `${f}: couldn't read file`;
	}
});

console.log(counts.join('\n'));
```

&nbsp;


`process` also gives you access to environment variables. Environment veriables are used often to configure the behaviour of some aspect of your programm (without having to provide them on the command line every time).

For example, we might use environment variables to control weather or not we log debugging info or not with a variable `DEBUG`;

```js
const debug = process.env.DEBUG === '1' ?
	concole.log:
	function() {};

debug('visible if env var is set');
```

If the env var is set, the functino debug works, otherwise it runs an empty function.

&nbsp;



`process.cwd` tells you the current working directory. `processs.chdir` allows you to change it. 

For example, printing out the initial dir then changing it:

```js
console.log('Current dir: ${process.cwd()}');
process.chdir(__dirname);
console.log('New dir: ${process.cwd()}');
```
&nbsp;

&nbsp;

#### Operating System


```js
const os = require('os');

console.log("Hostname: " + os.hostname());		// prometheus
console.log("OS type: " + os.type());			// Linux
console.log("OS platform: " + os.platform());		// linux
console.log("OS relese: " + os.relese());		// 3.13.0-52-generic
console.log("OS uptime: " +
	(os.uptime()/60/60/24).toFixed(1) + 'days');	// 80.3 days
console.log("CPU architecture: " + os.arch());		// x64
console.log("Number of CPUs: " + os.spus().length);	// 1
console.log("Total memory: "+
	(os.totalmem()/1e6).toFixed(1) + 'MB');		// 1,42.3 MB
console.log("Free Memory: " +
	(os.freemem()/1e6).toFixed(1) + 'MB');		// 195.8 MB
```

&nbsp;

&nbsp;


#### Child Processes



The `child_process` module allows you to run another program, weather a node program, an exe in another language.

It has three primary functions: `exec`, `execFile` and `fork`. There are also synchronous versions of these functions (`execSync`, `execFileSync`, `forkSync`). `exec` and `execFile` can run any executable supported by your OS. `exex` invokes a shell. `execFile` allows you to execute an executable directly. `fork` allows you to execute other Node scripts. 

&nbsp;

We'll give an `exec` example, running _dir_:

```js
const exec = require('child_process').exec;

exec('dir', function(err, stdout, stderr) {
	if(err) return console.error('Error executing dir.');
	stdout = stdout.toString();	// convert Buffer to string
	console.log(stdout)
	stderr = stderr.toString();
	if(stderr !== '') {
		console.error('error');
		console.error(stderr);
	}
});
```

Because `exec` runs a shell, there is no need for a path. If you were running separately, you would need it.

The callback recieves two _Buffer_ objects for stdout (output) and stderr (error, if any).

See the [official docs](https://nodejs.org/api/child_process.html) for more info
	

&nbsp;

&nbsp;

#### Streams


A stream is an object that deals with data (async). They can be _read streams_, _write streams_, or both (_duplex stream_). An example might be a user typing at a keyboard, or a web service that has back-and-forth communication with a client. 

We'll start by creating a write stream:

```js
const ws = fs.createWriteStream('stream.txt', { encoding: 'utf8' });
ws.write('line 1\n');
ws.write('line 2\n');
ws.end();
```


A read stream is similar:

```js
const rs = fs.createReadStream('stream.txt', { encoding: 'utf8' });
rs.on('data', function(data) {
	console.log('>> data: ' + data.raplace('\n', '\\n'));
});
rs.on('end', function(data) {
	console.log('>> end');
});
```

If you want to take the data from one stream and transmit it into another, called _piping_, you can do:

```js
const rs = fs.createReadStream('stream.txt');
const ws = fs.createWriteStream('stream_copy.txt');

rs.pipe(ws);
```

Piping is common, you could pipe the ocntents if a file to a webserver's response, or you could pipe compressed data into a decompression engine.

&nbsp;

&nbsp;


#### Web Servers


The original purpose of Node was to create web servers.

The `http` module, or the secure `https` one provide a method called `createServer` that creates a simple webserver. All you have to do is provide it with a callback function that will handle incoming requests. To start the server, you call `listen` to a specific port:

```js
const http = require('http');

const server = http.createServer(function(req, res) {
	console.log(`${req.method} ${req.url}`);
	res.end("Hello world!");
});

const port = 8080;

server.listen(port, function() {
	// you can pass a callback to listen that lets you know
	// the server has started
	console.log('Server started on port ${port}');
});
```

The server is live on _http://localhost:8080_. 

Every visit to the website, there are two requests:

```
GET /
GET /favicon.ico
```

This is because browsers request an icon to display in the URL bar or tab.

You could sent the favicon.ico file through a stream like this:

```js
const server = http.createServer(function(req, res) {
	if(req.method === 'GET' && req.url === '/favicon.ico') {
		const fs = require('fs');
		fs.createReadStream('favicon.ico');
		fs.pipe(res);		// this replaces the call to 'end'
	} else {
		console.log(`${req.method} ${req.url}`);
		res.end("Hello world!");
	}
});
```





