---
author: CF
categories:
  - code
  - api
  - docs
comments: true
description: >-
  I ask the question because I assume it must be considered evilon the
  subsequent assumption that if i...
draft: false
image: >-
  images/2013-07-06-from-js-doc-to-github-pages-in-27-easy-steps-aka-api-documentation-considered-evil.jpeg
layout: post
title: >-
  From JsDoc to Github Pages in 27 easy steps (aka API Documentation Considered
  Evil)?
toc: true
---
    
I ask the question, because I assume it must be (considered evil)--on the subsequent assumption that if it were not (considered evil) that _someone_, **somewhere**, somehow would have made it easy. I had a simple vision: take JsDocs from my [project](https://github.com/somecallmechief/oj), convert them to an [API document](https://github.com/jsdoc3/jsdoc), publish that document to my [Github pages](http://somecallmechief.github.io/oj/) place.    
    
Impossible is [nothing](http://www.youtube.com/watch?v=R7Kboormk3Y).    
    
## The short version    
    
1. Document your code using the [JsDoc v3 API](http://usejsdoc.org/).    
   1. Follow this API with religious fervor. It is itself not well-documented.    
   1. The use (or omission) of certain tags can have undocumented side effects. The old [JsDoc v2 Toolkit](https://code.google.com/p/jsdoc-toolkit/wiki/TagReference) is still valuable as a reference.    
   1. JsDoc will emit very few exceptions, other than raw syntax exceptions. You can call the jsdoc NPM directly with the --debug flag to hook into the Rhino debugger, but this didn't help me identify the issues.    
   1. I finally stumbled on [a post by Simon William](http://www.kajabity.com/2012/02/how-i-introduced-jsdoc-into-a-javascript-project-and-found-my-eclipse-outline/), which gave me the clues I needed to [find the faults in OJ's documentation](https://github.com/krampstudio/grunt-jsdoc-plugin/issues/46).    
1. Install the [grunt-jsdoc-plugin](https://github.com/krampstudio/grunt-jsdoc-plugin). Note: this plugin is a thin wrapper around the [jsdoc](https://npmjs.org/package/jsdoc) NPM. Nearly every fault you experience will result from jsdoc, not from the grunt-jsdoc-plugin.    
1. [+Eugene Krevenets](http://plus.google.com/109947886575868463460) has a good starter [post on how to extend this base task](http://pressanykeytocreate.blogspot.com/2013/05/creation-of-documentation-for.html) in order to skin the 1.put. This will get you closer to controlling the final skin of your API.    
1. If you use Eugene's steps (which I recommend), there is a gotchya inside the configuration file ".jsdocrc". The path of the plugins you specify will use either [a relative or an absolute path](http://usejsdoc.org/about-plugins.html), which as implemented is actually the worst of both worlds.    
1. Configure your grunt-jsdoc task to use [DocStrap](https://github.com/terryweiss/docstrap) for theming. This is a set of Boostrap themes configured to work on jsdoc output.    
1. I have not found a grunt task to automate this, so opted simply to copy the DocStrap project into a folder of my own project and link to it internally. The CSS styling is not as advertised, but it's better than trying to build my own 1.nt task to solve the problem.    
1. Sync the API doc output with Github using the Grunt [githubPages](https://github.com/thanpolas/grunt-github-pages) task. This was actually straightforward and just worked out of the box.    
    
## The long version    
    
A brief backstory. Early last year, we were expanding the team at ChemSW, and I wanted to be able to machine generate documentation from the the XML code comments across our various .NET projects. Aside: I had to look the date up, assuming it was years and years ago--shocking to find in the cold, hard, unrelenting truth of code commits that it was really just July, 01 2012 when I made my first commit on the subject. In spare time over 6-8 weeks or so, I scoured the inter-tubes for a tool which would do such a thing.    
    
After all, Microsoft does this and they make .NET. How hard could it be? [Hard](http://stackoverflow.com/questions/665925/what-are-some-good-net-code-documentation-tools). *Note: Supposedly GhostDoc could (presumably still does) this; but I'm not interested in negotiating purchase authority.* Most paths still lead to [Sandcastle](http://www.codeplex.com/Sandcastle), or one of the many forks thereof. As best I remember, Sandcastle was a solution to problems which haven't been imagined yet. In the end, I finally found a tool called [Doco](http://docu.jagregory.com/), which I was able to [fork](https://github.com/somecallmechief/docu) and make use of. All of this, of course, was for .NET only and didn't include documentation for JavaScript.    
    
For JavaScript, I chose the VsDoc style of documentation, because it purported to support some form of IntelliSense in Visual Studio. It does, and that feature works great until you add your 2nd JS file to the project.    
    
So back to the present. A theme emerges. I recently started breaking new ground on OJ, and I wanted to generate API documentation. I did some research, and the Internet gave me full faith and confidence that JsDoc was finally the format of choice and machines were ready to eat it and spew out well formed HTML.    
    
A few things have happened since then. First, the Chrome Dev team has made it drop-dead simple to debug your CoffeeScript and IcedScript and SASS and Less inside the dev tools. Second, Microsoft abandoned VsDocs when they embraced their own TypeScript. Third, since Grunt is now a de facto standard all its own, it's easier than ever to write your own niche NodeJs task and parse a few thousands files according to whatever regex you want. So more languages, more tools, more variation and a lower-than-ever bar to entry to roll-your-own solutions.    
    
For the past week, I have wrestled with getting various tools to comply with my core requirement: go make me some API docs. I started with the one and only jsdoc Grunt plugin, jsdoc. [It didn't work](https://github.com/krampstudio/grunt-jsdoc-plugin/issues/46). If you search the [Grunt plugins](http://gruntjs.com/plugins/doc) for 'doc', I think you'll find a long list of other well meaning tools, each of which in their own, niche ways also don't work.    
    
There's [docco](https://npmjs.org/package/grunt-docco2), a tool which dutifully generates a beautiful UI representing your standard comments next to code--it also mangles your actual code documentation comments with the associated code. I get that it's easy to parse out all lines starting with "//" and that it's hard to parse start "/\*\*" and end "\*/", but it does not mean I'm interested in the solution to the easy problem.    
    
There's [apidoc](https://npmjs.org/package/grunt-apidoc), a tool which promises to generate the beautiful API docs I want, using a jsdoc-like syntax. In almost every way, the semantics for this documentation is jsdoc, except every tag begins "api" + {tagName}, and it ignores all other tags.    
    
After vetting nearly every other tool out there, I circled back to grunt-jsdoc-plugin and filed a bug. Just 2 days later, and I finally have the problem clearly identified. My code is not explicit enough for JsDoc, by itself, to parse.    
    
```js    
/\*\*    
 \* Method to do something    
 \* @return {Array} An array    
\*/    
Object.defineProperty(nameSpace, 'method', {    
 value:    
  function () {    
    
  }    
});    
    
```    
    
In plain JavaScript, I'm simply writing:    
    
```js    
/\*\*    
 \* Method to do something    
 \* @return {Array} An array    
\*/    
nameSpace.method = function () {}    
```    
    
which JsDoc knows how to interpolate as it parses the AST. In my case, by using Object.defineProperty, I am "obfuscating" the assignment, the name of the property assigned and the type of the value being assigned, JsDoc iterates over this block and sees nothing to report. In this case, it is possible to instruct JsDoc what to do by defining the appropriate tags:    
    
```js    
/\*\*    
 \* @desc Method to do something    
 \* @name method    
 \* @return {Array} An array    
 \* @memberOf nameSpace    
\*/    
Object.defineProperty(nameSpace, 'method', {    
 value:    
  function () {    
    
  }    
});    
    
```    
    
Presto.    
    
Still. This time I solved the problem the traditional "right" way. By figuring out how to make the existing, seemingly proven tools work. I still have to refactor every code comment in my library before it will begin appearing in the documentation. Would it have been faster to grunt-force my own solution? Maybe next time.    
