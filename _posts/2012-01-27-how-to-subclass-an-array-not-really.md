---
author: CF
categories:
  - class
  - code
  - array
  - subclass
  - mistake
comments: true
description: >-
  Update I don't recommend following the advice in this post The 5minuteago
  version of my self was hat...
draft: false
image: images/2012-01-27-how-to-subclass-an-array-not-really.png
layout: post
title: How to Subclass an Array (Not Really)
toc: false
---
    
**Update: I don't recommend following the advice in this post**. The 5-minute-ago version of my self was hatsy, arrogant, foolish and a bad dinner guest. [I recommend the recently updated version of this post from the 1-minute-ago version of my self](http://hiking.luddites.me/2013/03/revisiting-how-to-subclass-array-really.html). He seems to be more well mannered and even keeled--I like him. However, if you're interested in what advice I used to be capable of delivering, please go on.    
    
If you've ever written more than a few lines of JavaScript, you've probably instanced and iterated an Array or two. As you've done this, you may have even thought to yourself, "[Gee willikers](http://answers.yahoo.com/question/index?qid=20080921002559AAcxT82), wouldn't it be keen to hook a contains() method on the Array prototype to simplify that awful indexOf(val) !== -1 [shenanigans](http://en.wikipedia.org/wiki/Shenanigans)?"    
    
You may have even tried to simply extend the native Array prototype. Life would have been grand until you needed your JavaScript to work in IE.    
    
From there, if you're at all like me, you might have thought: "This is friggin' JavaScript! Everything is mutable; everything is extensible! I'll just **_subclass_** the native Array type." The [subsequent journey through Google would then lead you to believe that the problem of subclassing Array is so hard, so frought with peril, so dangerous](https://www.google.com/webhp?sourceid=chrome-instant&ie=UTF-8&ion=1#sclient=psy-ab&hl=en&qscrl=1&site=webhp&source=hp&q=subclass%20array%20javascript&pbx=1&oq=&aq=&aqi=&aql=&gs_sm=&gs_upl=&qscrl=1&bav=on.2,or.r_gc.r_pw.r_cp.,cf.osb&fp=c5f41959aba83848&ion=1&biw=1628&bih=965&ion=1&pf=p&pdl=500) that no one could ever do it and do it right.    
    
And if you followed any of the advice that Herr Google had to offer, you would be right. And then you watched Douglas Crockford's [On JavaScript](http://www.yuiblog.com/crockford/) series and hit [Part III: Function the Ultimate](http://www.youtube.com/watch?v=ya4UHuXNygM&feature=related). Suddenly, it becomes much, much simpler.    
    
We now have an array() function which operates just like a plain vanilla, JavaScript Array (push, pop, splice and all intact), addressable by index with a properly functioning length property...and which contains a contains() method.    
    
To do this, we didn't need 'this' or 'new', and we don't have to touch 'prototype' at all if we wanted to interpret 'arguments' differently (though Array.prototype.slice.apply is probably the most efficient way to do it).    
    
This prototypical class model is, in my opinion, much easier to implement, read and debug than any of the blood, sweat and tear infused models suggested around the 'tubes.    
    
As always, your mileage may vary.    
