---
author: CF
categories:
  - code
  - measurements
  - speed
comments: true
description: >-
  [David Ungar]httpenwikipediaorgwikiDavidUngar cocreator of
  [Self]httpselflanguageorg the forefather ...
draft: false
image: images/2012-01-24-performance-determinism-vs.jpg
layout: post
title: Performance! Determinism. Vs?
toc: true
---
    
[David Ungar](http://en.wikipedia.org/wiki/David_Ungar) (co-creator of [Self](http://selflanguage.org/), the forefather to my favorite language, JavaScript) had an interesting talk at SPLASH 2011, which is [nicely summarized here](http://my-inner-voice.blogspot.com/2012/01/many-core-processors-everything-you.html) (post-talk [interview here](http://channel9.msdn.com/Blogs/Charles/SPLASH-2011-David-Ungar-Self-ManyCore-and-Embracing-Non-Determinism)).    
    
There are two predicate assumptions in this line of thinking. First, that in the end, we will always value performance over everything else (data integrity, reliability, determinism). Second, that we must sacrifice some level of determinism if we want to increase performance.    
    
The JavaScript example is a good one.    
    
"Why would you write critical business logic in an untyped language?"    
    
"Because, by sacrificing static typing, you can achieve performance that is otherwise impossible to attain."    
    
Of course, JavaScript itself [isn't even the Wild West](http://hacks.mozilla.org/2012/01/javascript-on-the-server-growing-the-node-js-community/). It's serial; it's single-threaded. It poses few of the suggested risks of operating in thousand core environment, where (if we are to believe Mr. Ungar) we must stare into the abyss.    
    
The line of thinking is provocative, and it's led me to wonder if it doesn't speak to something more basic: we must constantly challenge our assumptions about what is the most performant way to program.    
    
Recently, Node.js entered the tweet-o-sphere and has garnered enough attention that even Microsoft has built support for Node into IIS and their Azure platform. [Are they mad](http://www.hanselman.com/blog/InstallingAndRunningNodejsApplicationsWithinIISOnWindowsAreYouMad.aspx)?    
    
[In explaining the relevance of Node to broader technology groups](http://radar.oreilly.com/2011/07/what-is-node.html), Brett McLaughlin notes:    
    
"There's another web pattern at work here, and it's probably far more important than whether you use Node or not, and how evented your web applications are. It's simply this: use different solutions for different problems. Even better, use the right solution for a particular problem, regardless of whether that's the solution you've been using for all your other problems."    
    
That, to me, is the most elegant subversion of Mr. Ungar's thesis. We may have to abandon determinism to solve certain problems, but we can also abandon it in a deterministic way.    
