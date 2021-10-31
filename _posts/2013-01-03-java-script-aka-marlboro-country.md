---
author: CF
categories:
  - code
comments: true
description: >-
  Sometimes JavaScript land just feels like Marlboro Country The sales pitch
  certainly feels similar...
draft: false
image: images/2013-01-03-java-script-aka-marlboro-country.jpg
layout: post
title: JavaScript (aka Marlboro Country)
toc: true
---
    
Sometimes JavaScript land just feels like Marlboro Country. The sales pitch certainly feels similar:    
    
![marlboro country horse rider](https://github.com/crfroehlich/cdn/raw/main/images/marlboro_country_horse_rider.jpg)    
    
_Come to the hottest part of the United States. It's popular. It's everywhere. It's the future. Now: cover yourself in leather. Don't ask why. It's part of the feature set. Next: the temperature is 120 degrees, and you need to start a fire. It's not supposed to make sense, that's the point. You'll need to do this at noon every day. After about 10 days, you'll find yourself assaulting anyone \_not\_ starting noonday fires._    
![marlboro man](https://github.com/crfroehlich/cdn/raw/main/images/marlboro_man.jpg)    
_C'mon?! What's the worst that could happen?_    
_Imagine: right now, you could be riding across a brush fire.. or maybe it's a dust storm.. or maybe.. who knows? Everything's mutable! Ride across anything you want. Of course, you are limited to desert colors (obviously)._    
    
Lest you be confused, I love JavaScript. While the language as [specified](http://www.ecma-international.org/ecma-262/5.1/) is littered with nightmarish design flaws and obstacles reminiscent of trench warfare and carpet bombing (let's call them [WATs](https://www.destroyallsoftware.com/talks/wat)), JavaScript's Good Parts are so good you might be forgiven for forgetting about that elephant in the bathroom (aka IE). Yet.    
    
Yet and yet, the WATs [do also rise](http://www.workpump.com/bugcount/bugcount.html). And they rise.    
    
A few weeks ago, I began to set ink to digital paper to record the recipe for my new found [WAT](http://www.urbandictionary.com/define.php?term=wat&defid=3322419). There is a delightful tool for recording code demonstrations, [The Code Player](http://thecodeplayer.com/). While the demos presented are quite nice, I lost all 5 of my first drafts to bugs in the system. Still, you should keep an eye on it. I considered reverting to my old faithful, [JSFiddle](http://jsfiddle.net/), which is fantastic for mocking up and testing code but less suited for driving a walk through. In the end, syntax highlighting in the blog will have to must suffice.    
    
The municipal "we" begin with a simple function:    
    
```js    
function doVerbOnNoun(aThing) {    
  console.group(aThing);    
    
  //In theory, if(aThing) should be equivalent to if(true == aThing)    
  if (aThing) {    
    console.log('Verbing on ' + aThing);    
  } else {    
    console.log('Not verbing on ' + aThing);    
  }    
  console.groupEnd(aThing);    
}    
```    
    
A naïve developer, like myself, would assume that our guarding _if(truthy)_ would apply the [loose, truthy type coercions we have been trained to avoid](http://bonsaiden.github.com/JavaScript-Garden/#types.equality); and an undisciplined tester, like myself from a few moments ago, might almost be forgiven for missing the lawn for the dandelion. The following assertions execute as expected naïvely:    
    
```js    
//Strings:    
doVerbOnNoun('aThing'); //We verbed    
doVerbOnNoun(''); //Verb, we did not    
    
//Numbers:    
doVerbOnNoun(5); //We verbed    
doVerbOnNoun(NaN); //Verb, we did not    
    
//Booleans:    
doVerbOnNoun(true); //We verbed    
doVerbOnNoun(false); //Verb, we did not    
    
//Null and undefined:    
doVerbOnNoun(null); //Verb, we did not    
doVerbOnNoun(undefined); //Verb, we did not    
```    
    
But if the republic of we would but pause and stare but briefly into the abyss, it becomes immediately apparent that loose, truthy typely coerced evaluation is not happening here.    
    
```js    
doVerbOnNoun('a string of some sort'); //We verbed    
console.assert(true == 'a string of some sort', "'true' != 'a string of some sort'"); //Assertion fails    
    
doVerbOnNoun(5); //We verbed    
console.assert(true == 5, "'true' != 5"); //Assertion fails    
    
doVerbOnNoun('0'); //We verbed    
console.assert(true == '0', "'true' != '0'"); //Assertion fails    
```    
    
To my mind, this is irrational, unsettling and in possible violation of the laws of gravity; yet, it had never occurred to me that an _if('gimme shelter')_ statement would ever not be met as truthy. In fact, my mind was so far out of touch with the rules of the matrix, that I spent a few fevered seconds struggling with this paradox:    
    
```js    
doVerbOnNoun({}); //We verbed    
console.assert({} == true, '{} != true'); //Assertion fails    
console.assert({} == false, '{} != false'); //Assertion fails    
```    
    
Clearly, loose truthy evaluation is not part of the algorithm of the single parameter if() statement. The multinational we can exercise some futility by instrumenting doVerbOnNoun with some diagnostics:    
    
```js    
function doVerbOnNoun(aThing) {    
  console.group(aThing);    
    
  if (aThing) {    
    console.log('Verbing on ' + aThing);    
  } else {    
    console.log('Not verbing on ' + aThing);    
  }    
    
  console.log(!aThing, 'evaluating (!' + aThing + ')');    
  console.log(false != aThing, 'evaluating (' + aThing + ' != false)');    
  console.log(false != (false == aThing), 'evaluating (false != (false == ' + aThing + ')');    
  console.log(true == aThing, 'evaluating (' + aThing + ' == true)');    
    
  console.groupEnd(aThing);    
}    
```    
    
But none of the assertions align with the evaluation of the _if()_ statement 100% of the time. So what is actually happening? Pure, unbiased, unmitigated, unabashed madness--that's what. [Section 12.5 of the ECMAScript specification](http://www.ecma-international.org/ecma-262/5.1/#sec-12.5) defines the if() statement as:    
    
> "The production IfStatement : if ( Expression ) Statement is evaluated as follows:    
> Let exprRef be the result of evaluating Expression.    
> If [ToBoolean](http://www.ecma-international.org/ecma-262/5.1/#sec-9.2)(GetValue(exprRef)) is false, return (normal, empty, empty).    
> Return the result of evaluating Statement."    
    
What of [ToBoolean](http://www.ecma-international.org/ecma-262/5.1/#sec-9.2), you ask (jaws clenched in fear, rage and agony)? It is sufficiently (albeit imperfectly) expressed as:    
    
```js    
var ToBoolean = function (val) {    
  return (    
    val !== false &&    
    val !== 0 &&    
    val !== '' &&    
    val !== null &&    
    val !== undefined &&    
    (typeof val !== 'number' || !isNaN(val))    
  );    
};    
```    
    
At least to the existential "us", this revelation is almost laughably ironic. The if() statement isn't loose or truthy and doesn't coerce type whatsoever, rather it simply casts whatever it is offered ToBoolean().    
    
So ends this rather unsatisfying diversion into JavaScript. Please don't let this sully your feelings on the language. Restore your faith and go watch [+Douglas Crockford](http://plus.google.com/118095276221607585885)'s [latest, most excellent talk on Monads and Gonads](http://www.youtube.com/watch?v=dkZFtimgAcM). It's worth every minute of your time.    
