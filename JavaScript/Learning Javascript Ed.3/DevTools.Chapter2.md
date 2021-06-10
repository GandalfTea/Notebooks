

## JS Dev Tools 

Common used tools in dev teams :
* Git
* Node
* Gulp (or Grunt)
* Babel (ES6 to ES5)
* ESLint

   &nbsp;

   

### ES6

ES6 is a new version of JS. 
It is not yet generally accepted, therefore it needs to be trans-compiled into ES5 to make sure it can run anywhere.

A cool table keeping track of how much of ES6 is supported by a browser is: https://kangax.github.io/compat-table/es6/

At present, chrome, firefox and Edge are at 98%, while Safari is 100%.
   
   &nbsp;

   
### npm

npm is the package manager for Node. It is also very used for browser dev. 


You can install two types of packages:
* global -- command line tools.
* local  -- project specific. 


To track local dependencies in the program, you can use the command:
```
npm init
```
This creates a _package.json_ file that contains all the local dependencies.

There are two types of dependencies, _dev_ dependencies and regular ones. The _dev_ ones are not needed for the app to run. 

In order to track dependencies, you should add the flags `--save` or `--save-dev` when installing a package.

A typical install therefore should be:
```
$ npm install --save underscore
```
   &nbsp;

   
### Gulp and Grunt

They automate the repetitive parts of the dev process. 

Install them:
```
$ npm install -g gulp
$ sudo install -g gulp
```

For each project, you need a local Gulp. From the project root, run:
```
npm install --save-dev gulp
```

Than you need to create the _Gulpdile_ (_gulpfile.js_):
```js
const gulp = require('gulp');
// Gulp dependencies go here
gulp.task('default', function() {
	// Gulp tasks go here
})
```

Verify that it can run successfully:
```
$ gulp
```
```
[16:16:28] Using gulpfile /home/.../gulpfile.js
[16:16:28] Starting 'default'...
[16:16:28] Finished 'default' after 68 µs
```

On Windows: Many npm packages have a dependency on Visual Studio Tools. You might need it.
   
   &nbsp;

   
### Project Structure

For most of the book, the source code will be in a _es6_ directory. 
Because many projects include both backend (Node) and frontend (browser), backend will be in _es6_ while frontend will be in _public/es6_. 

ES5 code will be in a directory called _dist_ (distribution).

It will look like this:
```
.git
.gitignore

package.json
node_modules

es6
dist

public /
		es6/
		dist/
```

&nbsp;


### Transcompilers (not needed anymore)

Most popular at book time are _Babel_ and _Traceur_. 
Most popular at time of writing are ...

To install Babel:
```
$ npm install --save-dev babel-preset-es2015
```
Then, in project root, we create a file _.babelrc_ with the contents:
```
{ "presets" : ["es2015"] }
```
   
&nbsp;

   
#### Running Babel with Gulp

We'll convert all code in _es6_ and _public/es6_ to ES5 in _dist_ and _public/dist_.
Start by installing:

```
$ npm install --save-dev gulp-babel
```

Then edit the _gulpfile.js_:

```js
const gulp = require('gulp');
const babel = require('gulp-babel');

gulp.task('default', function() {

	// Node source
	gulp.src("es6/**/*.js")
		.pipe(babel())
		.pipe(gulp.dest("dist"));

	// Browser source
	gulp.src("public/es6/**/*.js")
		.pipe(babel())
		.pipe(gulp.dest("public/dist"));
});
```

This is a sample ES6 file to try it out:

```js
// es6 feature: block-scoped "let" declaration
const sequence = [
	{ subject: 'JavaScript', verb: 'is', object: 'great' },
	{ subject: 'Elephants', verb: "are", object: 'large' },
];
// es6 feature: object destruction
function say({subject, verb, object}) {
	//es6 feature: templait strings
	// note that quotes below are backticks(`), not single quotes (')
	console.log(`${subject} ${verb} ${object}`);
}
// es6 feature : for..of
for(let s of sequence) {
	say(s);
}
```

Create a copy of this file in _public/es6_ and type
```
$ gulp
```

When it's done, the new file, _test.js_ will be in _public/dist_.

In the book, they try to run the es6 file in Node and fail.
It runs now, in 2021.
    
&nbsp;

     
### Linting 

Linting looks at your code and tells you if you make common mistakes (like an IDE)

Install one:
```
$ npm install -g eslint
```
You need to create a _.eslintrc_ file for the project. 
To do this you can run:
```
$ eslint --init
```
It will ask you common questions to establish the type of project. 

You should add eslint to the Gulp file to check at build:

```
$ npm install --save-dev gulp-eslint
```

Then, the _gulpfile.js_:


```js
const gulp = require('gulp');
const babel = require('gulp-babel');
const eslint = require('gulp-eslint');

gulp.task('default', function() {
	
	// Run ESLint
	gulp.src(["es6/**/*.js", "public/es6/**/*.js"])
		.pipe(eslint())
		.pipe(eslint.format());

	// Node source
	gulp.src("es6/**/*.js")
		.pipe(babel())
		.pipe(gulp.dest("dist"));

	// Browser source
	gulp.src("public/es6/**/*.js")
		.pipe(babel())
		.pipe(gulp.dest("public/dist"));
});
```

Simply run gulp:
```
$ gulp
```
```
[15:04:16] Using gulpfile ~/git/gulpfile.js
[15:04:16] Starting 'default'...
[15:04:16] Finished 'default' after 84 ms
[15:04:16]
/home/ethan/lj/es6/test.js
	4:59 error Unexpected trailing comma comma-dangle
	9:5 error Unexpected console statement no-console

2 problems (2 errors, 0 warnings)
```

The trailing comma is a stylistic choice. You can edit the eslint to ignore it.i
In the _.eslintrc_ file:

```js
{
	"rules": {
		/* changed comma-dangle default...ironically,
			we can't use a dangling comma here because
			this is a JSON file. */
		"comma-dangle": [
			2,
			"always-multiline"
		],
		"indent": [
			2,
			4
		],
		/* ... */
```


You can configure lots of runes in the file. 

