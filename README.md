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

Next, set up a directory and initialize a new npm project.

`mkdir webpack-demystified`
`cd webpack-demystified`
`git init`

Take a minute to make your initial commit to github.







### Resources
[Awesome Webpack Blog Post](http://code.tutsplus.com/series/introduction-to-webpack--cms-983)
[Comparing Browserify/Grunt/Gulp/Webpack](https://npmcompare.com/compare/browserify,grunt,gulp,webpack)
