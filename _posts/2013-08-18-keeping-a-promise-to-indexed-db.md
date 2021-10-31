---
author: CF
categories:
  - persistence
  - zen
  - code
  - promises
  - lïf
comments: true
description: "APIs are hellish things inconstant moons inconsistent interfaces intolerable\_incompatibility If you ..."
draft: false
image: images/2013-08-18-keeping-a-promise-to-indexed-db.jpg
layout: post
title: Keeping a Promise to IndexedDb
toc: true
---
    
APIs are hellish things: inconstant moons, inconsistent interfaces, intolerable incompatibility. If you have not watched [Bret Victor's latest epic](http://worrydream.com/dbx), I do recommend it. As he channels the spirit of innovation from the 1970s, he notes that it would be horrible if, 40 years from then, the only way two systems could communicate were through predefined and agreed upon interfaces. Indeed. What a terrible thing that would be it is.    
    
As I continue to build out my prototype for [OJ's qDb](https://github.com/somecallmechief/oj-qdb) component (trying to fulfill [my promise to IndexedDb](http://hiking.luddites.me/2013/06/a-promise-to-indexeddb.html)), I have done a few of the obvious things. I automated the database connection in the constructor as [+Kyaw Tun](http://plus.google.com/115052908021360451116) recommended. I looked at [+Taylor Buley](http://plus.google.com/108530078008635144092)'s own [library](https://github.com/editor/indb) and make some tweaks accordingly. I have tested inserting up to a million records and selecting from the resulting object stores. There are some memory leaks to address and a few other performance issues before I'd recommend qDb on very, very large datasets--but it is in an OK state.    
    
Yet, as pleased as I am with the abstraction layer that I built, I feel a growing sense of frustration in this class of problem solving.    
    
**First**, as OJ has grown, it has become unwieldy. I finally decided to begin chunking it out into separate projects, which can each have their own test suites. They can be fully independent and therefore fully modular from OJ's point of view. Currently, I have:    
    
- [OJ](https://github.com/somecallmechief/oj)    
  - [OJ-NameSpace](https://github.com/somecallmechief/oj-namespace)    
  - [OJ-qDb](https://github.com/somecallmechief/oj-qdb)    
  - [OJ-SQL](https://github.com/somecallmechief/oj-sql)    
    
This is nice. I can register each of these as NPM packages and list them as dependencies of OJ in its NPM config. The projects get versioned independently, greater decoupling them. And there was much rejoicing.    
    
**Second**, as OJ has grown, it has become more tightly coupled internally. I personally detest the module pattern introduced in ES6, and I have never liked RequireJs or CommonJs's module patterns. That is a rant for another day. I do very much like the idea of defining all dependencies of a class up front, and fortunately it is easy to do both that and to use the namespace pattern (which I far prefer), by using Promises to solve the dependency management.    
    
I have wanted to blog directly at this feature, but I felt I needed to start at the beginning. I think [Grunt](http://gruntjs.com/) is a critical, essential tool of the language and Grunt is necessary to solve this problem. I began [introducing a part of my own migration to Grunt](http://hiking.luddites.me/2013/07/making-sense-of-grunt-importing.html) and the [churn required to get your own documentation generated from code](http://hiking.luddites.me/2013/07/api-documentation-considered-evil.html). I intend to follow this through my own namespacing pattern, into Promises and out to utopia. Not today.    
    
**Third**, as I have developed interfaces with more and more tools, I am increasingly aware of the fragility and incompatibility of the technologies we develop. Even between my own [raw, JavaScript object SQL library](http://hiking.luddites.me/2013/03/currying-favor-with-partial-application.html) and my own IndexedDb abstraction, the two have completely different interfaces. I have been refactoring my [Quips](http://fogbugz.stackexchange.com/questions/7350/implementing-bugzilla-style-quips-using-fogbugz-plugin-architecture) [plugin](https://github.com/somecallmechief/quips) to use [Firebase](https://www.firebase.com/), whose database API is completely different than anything I have already written (that's not a complaint, Firebase is very awesome and looks quite promising).    
    
Neither anticipated the other. And that is a sin. A sin with a capital _In_.    
    
Bret Victor notes that if two systems cannot learn how to communicate with each other (without so called "API"s) that they cannot scale or survive. I think there is wisdom in this observation, and I think part of the answer is right in front of us.    
    
A Promise is an agreement to try to do one thing. That thing can only succeed or fail (and potentially never do either). We know how to communicate Promises--within JavaScript, [Q](https://github.com/kriskowal/q) does a marvelous job transmogrifying loosely compliant promises into a standardized promises. Across languages, services, applications, architectures--we should think about agreeing upon, if nothing else, what it means to be a Promise and whether we agree to support it.    
    
It is not a complete solution to the challenge of connecting interfaces, but I promise it would help.    
