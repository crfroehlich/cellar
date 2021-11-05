---
author: CF
categories:
  - code
comments: true
description: "In the course of building out my SQL prototype..."
draft: false
image: images/2013-03-17-revisiting-how-to-subclass-an-array-really.jpg
layout: post
subtitle: ' How to Subclass an Array (Really)'
title: 'Revisiting: How to Subclass an Array (Really)'
toc: false
---
    
_Update: [+Axel Rauschmayer](http://plus.google.com/110516491705475800224) has an [even more succinct post on the subject](http://www.2ality.com/2013/03/subclassing-builtins-es6.html), which I highly recommend._    
    
In the course of building out my [SQL prototype](http://hiking.luddites.me/2013/03/currying-favor-with-partial-application.html), it's immediately obvious that I have to touch the Array prototype. You could write layers of abstraction to get around this, but in my opinion it is not worth the engineering effort [when extending the prototype is cleaner and low risk](http://perfectionkills.com/extending-built-in-native-objects-evil-or-not/). Still, we are talking about Array--an object which already has inconsistencies on older browsers and which is ever expanding at the ES spec level, so the idea of subclassing an Array is nice.    
    
[Despite my own argument to the contrary over a year ago](http://hiking.luddites.me/2012/01/how-real-persons-subclass-array.html), I don't think it possible or wise to try Array subclassing. First, let's look at the code I wrote back then:    
    
```js    
var array = function () {    
  var retArray = Array.prototype.slice.apply(arguments, 0);    
    
  retArray.contains =    
    retArray.contains ||    
    function (value) {    
      return retArray.indexOf(value) != -1;    
    };    
    
  return retArray;    
};    
```    
    
This has two problems. First, it doesn't execute. We'll get a type exception on the first line. The code needs to be:    
    
```js    
var array = function () {    
  var slice = Array.prototype.slice;    
  var retArray = slice.call(arguments, 0);    
    
  retArray.contains =    
    retArray.contains ||    
    function (value) {    
      return retArray.indexOf(value) != -1;    
    };    
    
  return retArray;    
};    
```    
    
It's important to understand the [difference between call and apply](http://stackoverflow.com/questions/1986896/what-is-the-difference-between-call-and-apply). While this code does return a new instance of an array with the new contains method--it hasn't actually subclassed. I've polluted the Array.prototype with my new method. In order to actually create a new subclass, you first need an abstraction to help think about prototypical inheritance.  Let's define it as:    
    
```js    
Object.defineProperty(Function.prototype, 'inheritsFrom', {    
  value: function (parentClassOrObject) {    
    if (parentClassOrObject.constructor === Function) {    
      //Normal Inheritance    
      this.prototype = new parentClassOrObject();    
      this.prototype.constructor = this;    
      this.prototype.parent = parentClassOrObject.prototype;    
    } else {    
      //Pure Virtual Inheritance    
      this.prototype = parentClassOrObject;    
      this.prototype.constructor = this;    
      this.prototype.parent = parentClassOrObject;    
    }    
    return this;    
  },    
});    
```    
    
Function inheritsFrom takes in an object and returns a 'this' which has been scoped as a derived class of the parentClassOrObject. This kind of prototype management is one of the reasons that it is harder (at least for me) to reason with this model. But now we have an abstraction to take care of this portion of the headache for us. Let's write the method to actually instance a new subclass:    
    
```js    
function makeSubClass(inheritsFrom, constructorCallBack) {    
  //Define the method    
  var ret = function () {    
    //The body of the constructor    
    var slice = Array.prototype.slice;    
    var args = slice.call(arguments, 0);    
    try {    
      if (inheritsFrom) {    
        inheritsFrom.apply(this, args);    
      }    
      //Optional callBack if we want to inject our own logic on construction    
      if (constructorCallBack) {    
        constructorCallBack.apply(this, args);    
      }    
    } catch (e) {    
      console.error(e);    
    }    
  };    
  //Do the subclassing    
  if (inheritsFrom) {    
    ret.inheritsFrom(inheritsFrom);    
  }    
  return ret;    
}    
```    
    
In a lot of use cases, this pattern will work just fine. And if you were to begin playing a new array subclass instanced in this way, it would largely behave normally.    
    
```js    
var nuArray = makeSubClass(Array);    
var nuInst = new nuArray();    
nuInst.push(1);    
nuInst\[0\] === 1; //true    
nuInst.length === 1; //true    
```    
    
But you may begin to notice the drawbacks. nuArray must be instanced with the new keyword, and it can't be instanced with data.    
    
```js    
var nuArray = makeSubClass(Array);    
var nuInst2 = new nuArray(1,2,3);    
nuInst2.length === 0; //true?!?    
nuInst2\[0\] === undefined; //true?!?    
//Try adding data by index    
nuInst2\[0\] = 1;    
nuInst2\[0\] === 1; //true    
nuInst2.length === 0; //true?1?    
```    
    
And from here, the experience continues to degrade. Using most of the Array mutator methods and all of the Array iterator methods will operate on and return Array instances--not instances of your subclass. You'll quickly find instance mutation to be rampant and unpredictable.    
    
You can continue down this path and try implementing your own overrides as callbacks. You can get really, really clever with this stuff; but ultimately, in my opinion--as written, Array was never intended to be the parent of a derived class. Just [don't go in that pool](http://www.youtube.com/watch?v=6CY_HGl6W2U).    
    
Embrace the extension of native objects, because that use case was clearly planned from the start.    
    
_\--As always, everything I write, in whatever language I write it, is fully released to the public domain._    
