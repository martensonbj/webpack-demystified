# Webpack Demystified

### Game Plan:
  - #### Part 1:
    - Understand what Webpack is, and why it is useful
    - Get an overview of Loaders and Plugins
  - #### Part 2:
    - Build a basic Webpack file from scratch
  - #### Part 2:
    - Realize that Webpack is like your organized but slightly finicky butler taking care of everything for you (as long as you give them the right instructions).

### Part 1: Big Picture

#### The Purpose of Build Tools
Let's say you're building a game in JavaScript and you're new to this whole coding thing. Then let's say that your HTML file looks like this:  
    ??? SCREENSHOT OF SHITTY HTML FILE

All of these JS files need to be loaded by your browser in a certain order, after the HTML is ready to go, or maybe before, depending on some details.   

All of the CSS files need to be applied, but not until the SASS compiler has done it's job.  

And don't forget about the images or static asset files - those need to be pulled form the CSS files or maybe from the HTML code and stuck in their appropriate places relative to where the browser will render them.  

All of that is a lot of stuff to worry about not only for the developer, but also for the browser. Debugging what went wrong can result in a bad time. Luckily for us, a variety of build tools have been developed that do all of the organizing for us.

Some of the main buzz words of you may have heard tossed around include Gulp, Grunt, or Webpack. These can have various labels like "Package Manager", "Bundler", or "Task Runner". There are quite a few differences between them, but they all share a primary responsibility: **Scan through all of your projects' dependencies, handle any task that needs to be done prior to production, and ship it off in a neat little package that is easy for a browser to digest.**


#### So What is Webpack?
Webpack is a node feature that digs through your JS modules, finds any dependencies, and spits out a single JS file that is ready for production.

What sets it apart is that it isn't limited to only handling your JavaScript files, it thinks of everything else like a module as well. With Webpack you have access to "Loaders" which pre-process your assets (like Fonts, SASS, Images, CSS, SVGs etc) and output exactly what your browser needs to know in the smallest package possible.

It also has this nifty thing called the `webpack-dev-server`  which tells your project to keep an eye on any changes, automatically refreshing your browser whenever it detects action.

In other words, Webpack finds the starter file you've told it to use, and crawls through the tree of dependencies in the appropriate order. It then minifies, optimizes, and loads anything it needs to make the browser happy. As they always say, a happy browser is a happy developer.

##### Webpack In Code

So what does Webpack configuration look like?

At its most basic level, you'll see something like this:

```
module.exports = {
  entry: {
    main: "./index.js",
  },
  output: {
    filename: "index.bundle.js"
  },
  module: {
    loaders: [
      {test: /\.js$/, loaders: ['babel'], exclude: /node_modules/},
    ]
  }
}
```

This chunk of code tells Webpack to start at the `entry` point. Which we define as `index.js`. This is where we require any other js files that we need in our project. It will follow the tree of nested files and concatenate/minify/work some webpack magic and `output` one single manageable file with the name of `index.bundle.js` which is what you'll put in your `index.html` file.

#### What about `loaders`?

A loader is a per-file processor that deals with individual file types for you, but all within the same config file.

This means that you can tell Webpack to go find any SASS files and turn them into CSS. Or go find any image file and compress it before pushing it up to the browser. Or compile the CoffeeScript that you blacked out while writing into JS before production. And so on.

Think of Loaders like a middle step. This makes it possible for you as a developer to write your code in whatever language makes you happy, then when you say "Ok go push to production", Webpack is like "hang on a sec...let me organize all of this for you" and then transpiles everything into a neat little bundle for production.

#### Loaders In Code
Loaders take an array of objects that uses regex to specificy what file extensions it applies to and what loaders are used

```
module: {
  loaders: [
    {test: /\.js$/, loaders: ['babel'], exclude: /node_modules/},
    {test: /\.css$/, loaders: ['style', 'css']},
    {test: /\.scss$/, loader: "style!css!sass" },
  ],
}
```

*PRO TIP: Loaders run from right to left! So it will run the "sass" loader first, do its thing, then find the "css" loader, then  the "style" loader and run that.*

Now that we have a big picture of what Webpack IS, let's see it in action.

### Part 2: Code Along

#### Install Some Command Line Tools

We'll need to install some tools both locally and globally so we have access to them everywhere they're needed. Let's start with global installation.

`npm install -g webpack webpack-dev-server mocha`

This will allow us to type `webpack`, `webpack-dev-server`, and `mocha` directly in our terminal whenever we need access to them.

#### Start A Project and Setup Webpack

Next, set up a directory and start tracking changes with git.

`mkdir webpack-demystified`
`cd webpack-demystified`
`git init`

Then kick off `npm`.

`npm init`

You'll be guided through the `package.json` setup wizard. Fill in the prompts as you see fit, or hit `enter` to choose the default. Keep in mind these settings can all be changed later directly in the `package.json` file.

Finally, install the dependencies we know we need locally. These will be explained in a bit more detail later.

`npm install --save-dev webpack webpackdev-server mocha mocha-loader chai`

Before we push to github, we need to make sure we have our `.gitignore` file ready to go. Luckily, Node hooks us up with a default file that we can pull down with `curl`.

While in your project directory, copy this into your terminal:

`curl https://raw.githubusercontent.com/github/gitignore/master/Node.gitignore > .gitignore`

Check it out! We have a `.gitignore`.

Git add. Git commit. Git push.

#### Set Up The File Structure

Next, time to create a file structure to hold our code. Let's create a `lib` and a `test` directory:  
`mkdir lib test`

And get some empty files ready to go:
`touch lib/index.js test/index.js`

At this point our project directory looks something similar to this:

```
.git
lib/
  index.js
node_modules/
test/
  index.js
.gitignore
package.json
README.md
```

#### Set Up the HTML file

At this point we're ready to write some code, but we need to give our project a place to output our js files, in both our applicaiton and test suite, respectively.

`touch index.html test.html`

```
<!-- index.html -->

<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Webpack Demystified</title>
</head>
<body>
  <h1>Luke, I am your father.</h1>
  <script src="bundle.js"></script>
</body>
</html>

```

Notice the single `<script>` tag located before the closing `</body>` tag. This is the epicenter of webpack. Any JS file we write will be bundled along with any dependencies and sent to production as `bundle.js`.

Next let's set up our `test.html` file.

```
<!-- test.html -->

<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Webpack Demystified Tests</title>
</head>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

The script tag now uses the `test` bundle. Soon we will specify a `main` bundle for our `index.html` file, after this quick demo of Webpack in action.

Make a new file in your `lib` directory called `alert.js`.  

`touch lib/alert.js`

```
// alert.js

module.exports = () => {
  alert('WEBPACK!!!!!!!!!')
}
```

```
// index.js

var newAlert = require('./alert')
newAlert();
```

Now open up index.html

`open index.html`

....buzzkill. No alert. But this shouldn't be a surprise because we haven't actually told Webpack to bundle our stuff yet. Do that now:

`webpack lib/index.js bundle.js`

This tells webpack to use `lib/index.js` as its entry point, and output a file called `bundle.js` which is exactly what we tell our `html` page to look for.

You'll notice we now have a `bundle.js` file in our `lib` folder. Reopen `index.html`. BAM!

Also take a minute to look at the terminal output.

```
$ webpack lib/index.js bundle.js
Hash: 65979c2d78d4ec0e2f84
Version: webpack 1.13.1
Time: 56ms
    Asset     Size  Chunks             Chunk Names
bundle.js  1.57 kB       0  [emitted]  main
   [0] ./lib/index.js 47 bytes {0} [built]
   [1] ./lib/alert.js 55 bytes {0} [built]
```

Even though we only told it to look into `index.js`, it's running through all of the dependencies (in this case only `alert.js`) and emitting them into 1 file, `bundle.js`.

#### Time to Automate!

Having to type the Webpack commands every time you want to run your app is a pain in the butt. We can write a configuration file that will run every time we spin up our browser and do all of the things for us.

First lets make a quick change to our `html` files. Since we have both a main production environment and a testing environment, lets specify how webpack will output our js files accordingly.

In `index.html` change the script tag to be `<script src="main.bundle.js"></script>`.

Likewise, in `test.html` change the script tag to `<script src="test.bundle.js"></script>`

Now make our config file:  

`touch webpack.config.js`

In that file, add the following:

```
const path = require('path');

module.exports = {
  entry: {
    main: "./lib/index.js",
    test: "mocha!./test/index.js"
  },
  output: {
    path: __dirname,
    filename: "[name].bundle.js"
  }
}
```

Let's take a second to talk about what all of this is. We're telling Webpack that we want two separate bundles from different `entry` points. One for our `main` application, and one for our `test` suite (which is why we also needed to change how each of our html files were looking for their appropriately bundled javascript).  

The output will then determine whether it started form the main or test entry point, and output the filename accordingly.

The way things are now, we still need to tell Webpack to run using the command `webpack` and then open our `index.html` file. Not a HUGE deal, but still kind of a waste of time.

Luckily, there's a sweet deal called `webpack-dev-server` that we mentioned earlier. This will boot up a development server and run our configuration file and reload our changes anytime we refresh our browser. Try it out!

`webpack-dev-server`

Then visit `http://localhost:8080`

Make a change to your alert.js file and refresh your browser.

#### Writing Tests

In `test/index.js`, write a simple test.

```
const assert = require('chai').assert

describe('our test bundle', function () {
  it('should work', function () {
    assert(true)
    })
  })
```

Visit `http://localhost:8080/text.html`  

Note that just like in your `lib/index.js` file, you can require other test files within the entry point `test/index.js` file and Webpack will bundle for you. Simply use `require('./other-test-file')` like you would in any other context.  This is important since keeping test files simple and elegant is an crucial part of writing clean code.


#### A few changes to package.json
`package.json` makes it easier to run commands. Let's make a few changes so we can keep our scripts straight.

```
// package.json
...
"scripts": {
  "start": "./node_modules/webpack-dev-server/bin/webpack-dev-server.js",
  "build": "./node_modules/webpack/bin/webpack.js",
  "test": "./node_modules/mocha/bin/mocha"
},
...
```
This lets us use the commands `npm start` to fire up `webpack-dev-server`, `npm build` to package everything for production, and `npm test` to execute our testing suite.





### Resources
[Awesome Webpack Blog Post](http://code.tutsplus.com/series/introduction-to-webpack--cms-983)
[Comparing Browserify/Grunt/Gulp/Webpack](https://npmcompare.com/compare/browserify,grunt,gulp,webpack)
