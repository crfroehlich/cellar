---
author: CF
categories:
  - tools
  - code
comments: true
description: "Last year I saw\_[+Paul Irish]httpplusgooglecom113127438179392830442's introduction to [Yeoman]httpye..."
draft: false
image: images/2013-07-28-making-sense-of-grunt-importing-an-existing-project.jpg
layout: post
subtitle: ' Importing an Existing Project'
title: 'Making Sense of Grunt: Importing an Existing Project'
toc: false
---
    
Last year, I saw [+Paul Irish](http://plus.google.com/113127438179392830442)'s introduction to [Yeoman](http://yeoman.io/) in [the Google I/O 2012 session](http://www.youtube.com/watch?v=Mk-tFn2Ix6g), and I instantly knew that I was fundamentally doing something wrong. Yeoman did not exist yet; but it was easy to find Grunt, which was already close to being well baked. However. All of the documentation for [Grunt](http://gruntjs.com/), its cousin [Brunch](http://brunch.io/), and the now available Yeoman seems to be geared toward starting new projects.    
    
Not yet grokking how the whole system worked, I wanted a simple, straightforward explanation for migrating an existing project into Grunt. I have not yet found one, and a search for "import existing project into Grunt" still returns [my SO question for the same](http://stackoverflow.com/questions/11404971/import-an-existing-javascript-project-into-a-grunt-brunch-project) in the top 10. So, this is my guide to taking a project and making it grunt.    
    
I already knew my JavaScript project was becoming a mess, but I didn't yet know how to fix it. I had a few problems:    
    
1. I had to manually manage the dev and Production versions of all my HTML pages. Any changes to 3rd party libraries had to be changed in at least two places. Any JS file add/drop/renames had to be manually managed.    
1. I was using a pretty crude Perl script to assemble my source files and pass them into Google Closure Compiler. This, too, had to be manually managed.    
1. Google Closure Compiler hadn't kept up with the language. As I began to rely more heavily on features in EcmaScript 5 as well as polyfills from ES6, Closure Compiler couldn't keep up. I eventually had to rely on whitespace compilation only.    
1. Instructions to update Closure Compiler had to be manually propagated across the team.    
1. Repeat these issues with CSS compilation. I was using YUI Compressor.    
    
My system had multiple, parallel points of failure. It was fragile. Management was entirely manual. I saw the potential that Grunt had to offer, but it took me awhile to trial-and-error my way to yes. _NOTE: The [Grunt documentation](http://gruntjs.com/getting-started) is good, and I do recommend using it heavily._    
    
### Step 1: What is Grunt    
    
_[NodeJS](http://nodejs.org/), the server-side JavaScript platform, is the secret to this sauce and [NPM](https://npmjs.org/) is the package manager for Node (think Ruby RubyGems, Python pip, Perl PPM, Go... etc). Grunt is an npm package, which you'll install._    
    
At its core, Grunt is a task runner. You define a set of plain-vanilla, private functions that you want Grunt to execute. Then you define plain-vanilla JavaScript objects, which Grunt will then pass as parameters to these functions. Then you define a public superset of these functions that you want to allow Grunt to execute from the command-line. Then you can execute Grunt.    
    
1. Install [NodeJS](http://nodejs.org/)    
1. (if on Windows, I recommend using [Chocolatey](http://chocolatey.org/))    
    
```bash    
cinst nodejs.install    
```    
    
1. Globally install the Grunt command line ([global vs local](http://blog.nodejs.org/2011/03/23/npm-1-0-global-vs-local-installation/)):    
    
```bash    
npm install -g grunt-cli    
```    
    
### Step 2: Your Project is Now an NPM Package    
    
The former incarnation of my project was a sprawling mass of files and folders, but Grunt requires that your content be packageable for NPM. In the end, you will even need to use NPM to install your package. For now, this means designating a root folder and creating a package file. Initially, you can think of your content structured in two categories: the content that **you** modify vs the content **Grunt** modifies.    
    
1. Designate a root folder for your content _(e.g. /web/)_    
1. Copy your assets into an assets folder in the root _(e.g. /web/app/)_    
   1. It is customary to split assets according to type _(e.g. /web/app/css/, /web/app/js/, /web/app/img/, etc.)_    
1. Designate a folder for Grunt to output content _(e.g. /web/release/)_    
1. Create the NPM package file, named package.json _(e.g./web/package.json)_    
    
```json    
{    
  "name": "",    
  "title": "",    
  "description": "",    
  "version": "",    
  "homepage": "",    
  "author": {    
    "name": "",    
    "email": ""    
  },    
  "repository": {    
    "type": "",    
    "url": ""    
  },    
  "bugs": {    
    "url": ""    
  },    
  "licenses": \[{    
      "type": "",    
      "url": ""    
  }\],    
  "dependencies": {    
    "grunt": "0.4.1",    
    "grunt-contrib-clean": "latest",    
    "grunt-contrib-cssmin": "latest",    
    "grunt-contrib-uglify": "latest"    
  },    
  "devDependencies": {    
    
  },    
  "keywords": \[\],    
  "engines": {    
    "node": "0.8.x",    
    "npm": "1.1.x"    
  }    
}    
```    
    
Should you ever want to publish your package to NPM, the whole file should be fleshed out. For now, the most important properties are **dependencies** and **devDependencies**. [Dependencies](https://npmjs.org/doc/json.html#dependencies) are the names and versions of the NPM packages which must be installed for your project to execute. These are normally installed by calling    
    
```bash    
npm install  --save    
```    
    
[DevDependencies](https://npmjs.org/doc/json.html#devDependencies) are the optional packages you need only to run non-essential tasks, install by calling    
    
```bash    
npm install  --save-dev    
```    
    
Most of the common things you will want to do are defined in the context of Grunt plugins. Plugins are NPM packages tailored specifically to run as Grunt tasks.    
    
### Step 3: Identify Your Files    
    
Grunt has access to the file system, but it does not know which files to process or how to think about them, so you must define the set of files you want Grunt to operate on. There are various ways to do this, but I prefer managing files in a dedicated configuration file.    
    
1. Create an asset list _(e.g. /web/files.js)_    
    
```js    
  module.exports.app = \[    
      'app/js/NameSpace.js',    
      'app/js/\*\*/\*.js'    
  \];    
    
  module.exports.css = \[    
      'app/css/\*\*/\*.css'    
  \];    
    
  module.exports.vendorMin = \[    
      'vendor/js/modernizr-2.6.2.min.js',    
      'vendor/js/es5-shim.min.js',    
      'vendor/js/jquery-2.0.3.min.js',    
      'vendor/js/q.min.js'    
  \];    
    
  module.exports.vendor = \[    
      'vendor/js/modernizr-2.6.2.js',    
      'vendor/js/es5-shim.js',    
      'vendor/js/jquery-2.0.3.js',    
      'vendor/js/q.js'    
  \];    
    
  module.exports.images = \[    
      'app/img/\*\*/\*.png',    
      'app/img/\*\*/\*.gif'    
  \];    
```    
    
We'll later instruct Grunt to consume this file into a variable, and the \`modules.exports\` syntax will instruct Grunt to export each property as a member of the return object. Grunt also supports a range of globbing patterns--'\*\*' is the simplest way to include all files and folders beneath the path, recursively.    
    
### Step 4: Create the Gruntfile    
    
The Gruntfile is by far the most complex piece, so for the purpose of this guide, I'll focus on only three tasks: compiling JavaScript, compiling CSS and cleaning the release folder.    
    
1. Create a file named Gruntfile.js _(e.g. /web/Gruntfile.js)_    
    
```js    
/\*global module:false\*/    
module.exports = function (grunt) {    
    
    var files = require('./files'); //Load files.js into a local variable    
    
    grunt.initConfig({    
  files: files, //store the files variable inside the grunt config object    
    
  pkg: grunt.file.readJSON('package.json'),    
    
        clean: \['release'\],    
    
        cssmin: {    
            files: {    
                src: files.css,    
                dest: 'release/mycss.min.css'    
            }    
        },    
    
        uglify: {    
            files: {    
                src: files.app,    
                dest: 'release/myjs.min.js'    
            }    
        }    
    });    
    
    grunt.loadNpmTasks('grunt-contrib-clean');    
    grunt.loadNpmTasks('grunt-contrib-cssmin');    
    grunt.loadNpmTasks('grunt-contrib-uglify');    
    
    grunt.registerTask('compile', \['clean', 'cssmin', 'uglify'\]);    
};    
```    
    
I keep my Gruntfile broken into three purely conceptual sections. First, there is the variable initialization before calling \`_grunt.initConfig_\`. This just makes it clear that I am assigning some content outside of the Grunt init.    
    
Second, there is the required call to \`_grunt.initConfig_\`. This is the core of the file. All common task configuration is done here. The names of the properties in this object usually map to the names of the associated task to run.    
    
Third, there is the task initialization and task identification section. Here, \`_grunt.loadNpmTasks_\` is called to load a plugin for use in Grunt. This plugin must be installed and identified as a dependency in package.json. (installation happens automatically when you install your project). You can also define additional tasks to run by calling \`grunt.registerTask\`    
    
The [clean](https://npmjs.org/package/grunt-contrib-clean) task is the most straightforward. Given an array of folder names, it will delete their contents. [Cssmin](https://npmjs.org/package/grunt-contrib-cssmin) will concatenate and compile CSS files into a single, compressed file. Likewise, [uglify](https://npmjs.org/package/grunt-contrib-uglify) will take your JS files, concatenate and compile them into a minified file.    
    
My custom task, 'compile' simply cleans the 'release' folder and then compiles the CSS and JS back into release.    
    
### Step 5: Release    
    
Once the pieces are in place, putting them together is a one-two:    
    
1. cd into the project directory (e.g./web/)    
1. Install the project    
1. `npm install`    
1. Compile the project    
1. `grunt compile`    
    
I'm sure most of this was quite obvious to rest of the community, but it took me some considerable iteration to get it right. While it seems intuitive to me now, I thought it worth documenting from my own experience. You can see the entire [Grunt configuration](https://github.com/somecallmechief/oj/blob/master/Gruntfile.js) for my own project, [OJ](https://github.com/somecallmechief/oj/) on Github.    
    
These steps are enough to begin making headway, but they are by no means enough to get your entire project fully migrated into Grunt. Next time, I'll discuss how to dynamically generate my project's HTML using Grunt tasks.    
