---
author: CF
categories:
  - hiking
  - code
  - lïf
comments: true
description: The Trailhead...
draft: false
image: images/2011-12-28-38-000-steps-under-the-digital-wallpaper-of-the-sun.jpg
layout: post
title: '38,000 Steps Under the Digital Wallpaper of the Sun'
toc: false
---
    
## The Trailhead    
    
I've been hiking my desk for nearly a year now, and the road gets ever longer. I've had [a book published](http://www.amazon.com/Complete-Idiots-Guide-Android-Development/dp/1615641068). I've been promoted. We've [had a daughter](https://picasaweb.google.com/108988276571177665337/AntasiLands?authuser=0&feat=directlink), [survived a flood](https://picasaweb.google.com/108988276571177665337/TheGreatFlood?authuser=0&feat=directlink), lost and learned, and [survived baby kidney surgery](https://picasaweb.google.com/108988276571177665337/ThereAndBackAgain?authuser=0&feat=directlink). Eli more fully experienced his latest [Chanukah](https://picasaweb.google.com/108988276571177665337/20111226?authuser=0&feat=directlink), Winter Solstice and [Christmas](https://picasaweb.google.com/108988276571177665337/Christmas2011?authuser=0&feat=directlink). He survived both [Saint Nick](http://www.google.com/search?sourceid=chrome&ie=UTF-8&q=saint+nick+attacks&qscrl=1#pq=saint+nick+attacks&hl=en&cp=10&gs_id=21&xhr=t&q=santa+attack&qe=c2FudGEgYXR0YQ&qesig=NmOn6J806lYHErpOdEIjxg&pkc=AFgZ2tmxSTwayj3wHSOxjL6BcA6wgfzL7fRSKtrgp5gDyvKftJiqESq6b9yqyCNM2ICfv6tY-KIsKHzl33AiuFZO3pVZ9CH7Qw&pf=p&sclient=psy-ab&qscrl=1&source=hp&pbx=1&oq=santa+atta&aq=0&aqi=g3&aql=&gs_sm=&gs_upl=&bav=on.2,or.r_gc.r_pw.r_cp.,cf.osb&fp=a07b3761d35d504a&biw=1670&bih=827) and the [Solstice Squid](http://www.reverbnation.com/play_now/song_1553877). I've slept less this year than any year prior--including years in Iraq. In short, I've continued my hike up the Mountain of Lif.    
    
This year, I also learned JavaScript. Thanks [in no small part to the team at [ChemSW](http://www.chemsw.com/), [Alex Russel](http://www.youtube.com/watch?v=seX7jYI96GE), [Douglas Crockford](http://javascript.crockford.com/)* (and [far](http://bonsaiden.github.com/JavaScript-Garden)  [too](http://perfectionkills.com/)  [many](http://addyosmani.com/blog/)  [more](http://www.bennadel.com/) to [name](http://infrequently.org/) by [name](http://badassjs.com/)) and [others](http://www.html5rocks.com/en/)[who](http://metaphysicaldeveloper.wordpress.com/) dedicated their time to write, [blog](http://www.yuiblog.com/blog/), speak, evangelize, scrutinize and [mock](http://wtfjs.com/) the little language that could] to the tubes which connect us all, I've come to know and love the beauty and elegance that JavaScript has to offer.    
    
Even if you're not a programmer or even if you are and you're a JavaScript expert: give yourself a belated Holiday gift. [Watch episode 1 of Crockford's "On JavaScript" series. It's simply profound](http://www.yuiblog.com/blog/2010/02/03/video-crockonjs-1/).    
    
The brief primer (forgive the hubris) which follows is redundant with much of the work that the aforementioned have already produced. I have extended surprisingly (to me) little creative energy in adding my own material--much of this is taken verbatim from its respective sources; however, it does consolidate in one page what it has taken me a year to begin learning. I've tried to give attribution whenever I have borrowed from someone else--if I missed you, please let me know. If for no other reason that to learn by writing (it produced two books already), here is:    
    
## Hiking JavaScript, A Primer    
    
### Overview    
    
#### History    
    
[JavaScript](http://en.wikipedia.org/wiki/JavaScript)(TM) Oracle is a language developed under Netscape in 1995 by [Brendan Eich](http://en.wikipedia.org/wiki/Brendan_Eich) in about 8 days. Originally named LiveScript, JavaScript fuses the syntax of Java with the richness of [Self](http://en.wikipedia.org/wiki/Self_(programming_language)) and [Scheme](http://en.wikipedia.org/wiki/Scheme_(programming_language)) (a successor of LISP). It is a functional language which is easily adapted to a variety of programming styles. LiveScript was originally intended to be a proprietary language for Netscape. This design choice was one of the motivating factors for the [Browser Wars](http://en.wikipedia.org/wiki/Browser_wars) in the late 90s, early 00s. Microsoft reverse engineered LiveScript into JScript, and in their dedication, they preserved nearly every bug in the original version of the language. In response, Netscape took JavaScript to the W3C standards committee for ratification as a standard.  Trademark threats from then Java trademark owner Sun Micro forced the standard name to ECMAScript. ECMAScript 5 was ratified in 2009 and is often associated with HTML5 and CSS3 under the blanket "HTML5" alias. ES5 is fully supported by the latest versions of [Chrome, Safari, Opera and Firefox. Internet Explorer 9](http://kangax.github.com/es5-compat-table/) implements most of the standard, though [strict mode](http://www.yuiblog.com/blog/2010/12/14/strict-mode-is-coming-to-town/) is still not fully implemented in the pre-release versions of IE 10.    
    
#### Features    
    
JavaScript is a loosely typed language. Nearly everything in JavaScript inherits from the Object type. The notable exceptions are [null, undefined](http://bonsaiden.github.com/JavaScript-Garden/#core.undefined) and a limited number of reserved keyword operators like [typeof and instanceof (which should be used with caution)](http://bonsaiden.github.com/JavaScript-Garden/#types.typeof). JavaScript implements native types for String, Number, Date, Boolean, Array and Function. Functions in JavaScript are Objects. They are first-class, an feature derived from LISP/Scheme. This is _the_ feature that makes JavaScript one of the best modern programming languages.    
    
#### Compilation    
    
In the Neolithic era, JavaScript was interpreted at run-time, line-by-line, by the browser. [All modern browsers now compile](https://code.google.com/apis/v8/design.html#mach_code) _at least_ [some portion of loaded JavaScript into byte-code](http://stackoverflow.com/questions/889195/do-browsers-compile-and-cache-javascript).    
    
- [Compiled, Interpreted, Whatever](http://blogs.msdn.com/b/ptorr/archive/2003/09/14/56184.aspx)    
- [The JavaScript Engine](https://secure.wikimedia.org/wikipedia/en/wiki/JavaScript_engine)    
    
## The Language    
    
### Operators    
    
JavaScript supports a standard set of operators:    
    
#### Arithmetic    
    
|     |     |     |     |     |
| --- | --- | --- | --- | --- |
| `+` | `-` | `*` | `/` | `%` |
    
#### Comparison    
    
|      |       |      |       |     |      |     |      |
| ---- | ----- | ---- | ----- | --- | ---- | --- | ---- |
| `==` | `===` | `!=` | `!==` | `<` | `<=` | `>` | `>=` |
    
#### Logical    
    
|      |      |     |
| ---- | ---- | --- |
| `&&` | `||` | `!` |
    
#### Bitwise    
    
|     |     |     |      |       |      |
| --- | --- | --- | ---- | ----- | ---- |
| `&` | `|` | `^` | `>>` | `>>>` | `<<` |
    
#### Ternary    
    
|     |     |
| --- | --- |
| `?` | `:` |
    
### Equality    
    
[Never use single or double operator syntax!...!! Equality should always be evaluated using triple operator syntax](http://bonsaiden.github.com/JavaScript-Garden/#types.equality). For example:    
    
```cs    
const x = 'false';    
!x; // true    
x == false; // true    
x === false; // false    
```    
    
Single operator equality evaluation is only possible in the negative (`!`). Double operator evaluation can be either negative or positive (`!=` or `==`). Each perform type coercion PRIOR to equality evaluation.    
    
Unfortunately, greater than/less than operators perform type coercion as well.    
    
'1' > 0; // true    
```cs    
'1' > '0'; // true    
'a' > 1; // false    
'a' < 1; // false    
'a' > '1'; // true    
'a' > 'b'; // false    
```    
    
Beware!    
    
### Truthy/Falsey    
    
The following values are falsey in JavaScript, that is, type coercion will evaluate these to false:    
    
- `false`    
- `null`    
- `undefined`    
- `""` (empty string)    
- `0`    
- `NaN`    
    
All other values (including objects [that means functions]) are truthy: `0` and `false`. Type coercion will evaluate these to true.    
    
[Never use the auto-increment or auto-decrement syntax (++/--)](http://www.jslint.com/lint.html)!! The plus and minus operators both add and subtract as well as concatenate. This has the potential to inject unanticipated bugs, as + will inconsistently induce type coercion. The + rule: if (both operands are numbers) { then add them } else { convert them both to strings and concatenate them }.    
    
```cs    
const x = `${1}1`; // 11    
const y = 1 + +'1'; // 2    
const z = ++[[]][+[]] + [+[]]; // "10"    
```    
    
A plus/minus prefixed to a string is the right way to cast a string as a number. This type coercion is desired and expected. To avoid confusion, use parens to clearly communicate the intention of your code (when adding a coerced string to a number). [For elaboration on z, see](http://stackoverflow.com/questions/7202157/can-you-explain-why-10).    
    
When your intention is to increment use the += 1/-=1 metaphor.    
    
### typeof    
    
typeof returns unexpected results if handed null or an array. As such, it should be used with extreme caution. This is the return table for typeof:    
    
| Type      | typeof returns |
| --------- | -------------- |
| Object    | 'object'       |
| Function  | 'function'     |
| Array     | 'object'       |
| Number    | 'number'       |
| String    | 'string'       |
| Boolean   | 'boolean'      |
| Null      | 'object'       |
| undefined | 'undefined'    |
    
#### instanceof    
    
[The instanceof operator compares the constructors of its two operands. It is only useful when comparing custom objects. Used on native types, it is nearly as useless as the typeof operator.](http://bonsaiden.github.com/JavaScript-Garden/#types.instanceof)    
    
Custom Objects:    
    
```cs    
function Foo() {}    
function Bar() {}    
Bar.prototype = new Foo();    
new Bar() instanceof Bar; // true    
new Bar() instanceof Foo; // true    
Bar.prototype = Foo;    
// This just sets Bar.prototype to the function object Foo    
// But not to an actual instance of Foo    
new Bar() instanceof Foo; // false    
```    
    
Native Types:    
    
```cs    
new String('foo') instanceof String; // true    
new String('foo') instanceof Object; // true    
'foo' instanceof String; // false    
'foo' instanceof Object; // false    
```    
    
### Types    
    
All objects are declared either as variables or functions.    
    
```cs    
const number = 1;    
const string = 'string';    
const boolean = false;    
const date = new Date('10/13/2011');    
const array = [1, 2, 3];    
const object = { prop: 'prop' };    
function objFunction() {    
  alert('hello');    
} // equivalent to var objFunction = function() { alert('hello'); };    
```    
    
Type is not declared in variable declaration, [which has lead some to call JavaScript an "untyped" language](http://www.google.com/search?sourceid=chrome&ie=UTF-8&q=javascript+is+untyped&qscrl=1). This is not true. JavaScript is only loosely-typed. Type is inferred on assignment and a variable's type can be changed at any time by reassignment or modification of the object prototype.    
    
All types derived from Object have a few critical methods: constructor, prototype, hasOwnProperty and toString. In order to later determine a variable's type, use the prototype.toString() method.    
    
#### Object    
    
An object is a dynamic collection of properties. Every property has a key string that is unique within that object.    
    
Unfortunately, property keys must resolve to strings--a key cannot be an more complex object. To address the issue of string resolution for property names, Object properties can be accessed literally or by string:    
    
```c    
prop.key = '1';    
prop.key; // "1"    
    
prop.key = '1';    
prop.key; // "1"    
    
prop.key === prop.key; // true    
```    
    
Object properties can be get, set and deleted:    
    
```cs    
const newObject = { prop1: 'new prop' };    
// get    
const x = newObject.prop1;    
const y = newObject.prop1;    
// set    
newObject.prop1 = 'changed prop';    
newObject.prop1 = 'changed prop';    
// delete    
delete newObject.prop1;    
delete newObject.prop1;    
```    
    
Object literal notation:    
    
```cs    
// literal    
var myObject = { foo: bar };    
// explicit    
var myObject = Object.defineProperties(Object.create(Object.prototype), {    
  foo: {    
    value: bar,    
    writeable: true,    
    enumerable: true,    
    configurable: true,    
  },    
});    
```    
    
### Object.toString()    
    
Object exposes a toString() method. Do not use it.    
    
```cs    
const obj = { a: 1, b: 2 };    
// toString === bad    
obj.toString(); // "[object Object]"    
obj.toString(); // "[object Object]"    
// JSON === good    
JSON.stringify(obj); // "{"a":1,"b":2}"    
JSON.stringify(obj); // "{"a":1,"b":2}"    
```    
    
### String    
    
[The most idiotically named type in any language](http://stackoverflow.com/questions/880195/the-history-behind-the-definition-of-a-string), JavaScript implements a native type of String. String support is fully implemented in all current browsers (including IE 8). String's indexOf() method should not be confused with Array's. String also implements a length property, which is also not to be confused with Array. In the context of the type of String, length denotes the number of characters (including spaces) in a String object. String's implementation is designed to remove most of the feature of the Object type to simplify and reduce string operation complexity.    
    
#### String.length    
    
The string.length property determines the number of 16-bit characters in a string. Extended characters are counted as 2.    
    
The two most common errors with String manipulation occur out of its implementation of length.    
    
First, when passing strings into (particularly 3rd party) modules which makes logical assumptions based on the existence of length, strings will produce unexpected behavior.    
    
Second, if accidentally passed to a for() loop.    
    
```c    
for (const i in 'string') {    
  alert(i);    
}    
```    
    
ES5 does not implement forEach() on the String type, but it is still possible to iterate a string in a for loop due to the indexOf() method. Because each character in a string has a unique, linear index number--String must also have a hasOwnProperty() implementation to find the character at a particular index.    
    
```cs    
const str = 'string';    
str[0]; // 's'    
str.hasOwnProperty('s'); // false    
str.hasOwnProperty(0); // true    
```    
    
#### Methods    
    
String implements a variety of useful methods which operate much as their names suggest:    
    
- charAt    
- charCodeAt    
- compareLocale    
- concat    
- indexOf    
- lastIndexOf    
- localeCompare    
- match    
- replace    
- search    
- slice    
- split    
- substring    
- toLocaleLowerCase    
- toLocaleUpperCase    
- toLowerCase    
- toString    
- toUpperCase    
- trim    
- valueOf    
    
#### String.toString()    
    
Calling toString() on a string returns itself--which is exactly what you would expect.    
    
### Boolean    
    
The Boolean type has no widespread, documented issues. Boolean variables can only be a strict true or false. Boolean implements hasOwnProperty() but is not subject to any of the potential problems with string as it does not have indexOf() or length.    
    
```cs    
const t = true;    
t.toString(); // "true"    
t.toString(); // "true"    
```    
    
#### Boolean.toString()    
    
Boolean toString() works as expected.    
    
### Number    
    
Number is 64-bit floating point, similar to Double. There is no integer type. Division between two integers may produce a fractional result. Number also includes the special values NaN (not a number) and Infinity. The Number type in JavaScript is the most imperfectly implemented type.    
    
#### NaN    
    
Like most other languages:    
    
```cs    
(NaN === NaN)(    
  // false    
  NaN !== NaN,    
); // true    
```    
    
When evaluating for NaN, always use an isNaN() method, such as the one in jQuery.    
    
#### Floating Point    
    
The first problem with floating point based number systems is that they cannot accurately represent decimal fractions. This means that:    
    
`0.2+0.7===0.9 //false`    
    
The [Associative Law](http://en.wikipedia.org/wiki/Associativity) does not hold in JavaScript. This was not remedied in ES5 and will not likely be fixed anytime soon. As such:    
    
`(((a + b)+ c)+ d)!==((a + b)+(c + d))`    
    
Decimal computation in JavaScript is just bad. There is no way around it except to eliminate the decimal from math operations and return it later. This means that currency operations should begin with multiplying by 100 and end by dividing by the same. Decimal math should either be resolved server-side, or the server should eliminate decimals in communication with the client.    
    
#### Math    
    
[Math is not implemented on the Number type. It is its own object](http://www.w3schools.com/jsref/jsref_obj_math.asp), which is good and bad. Bad that basic math methods are not available as immediate extensions of the number type and the subsequent code construction becomes heavy. Good, because almost all of the Math object's methods are implemented correctly. Math implements constants for Pi and other common constant numbers as well as various methods for random(), log(), etc. Math is good--use it.    
    
#### Number.toString()    
    
Number correctly implements toString().    
    
```cs    
const num = 1;    
num.toString(); // "1"    
num.toString(); // "1"    
```    
    
#### Number Methods    
    
- toExponential    
- toLocaleString    
- toString    
- toFixed    
- toPrecision    
- valueOf    
    
### Date    
    
Date is the only type which really requires construction and assignment using the 'new' keyword. Dates cannot be easily inferred or coerced from String, Number or Object types--except in the case where the value of an Object property is a non-string literal date as sent from the server (in this case JSON.parse() can frequently cast the property value correctly as a date). So, date assignment is best done in the syntax:    
    
`var time =newDate('12:00');`    
    
#### Date.toString()    
    
Do not use the toString() method on Date types. Instead, use the [date/time locale methods](http://www.w3schools.com/jsref/jsref_obj_date.asp) to generate an appropriate date string: toLocaleString(), toLocaleDateString(), etc.    
    
#### Date Math    
    
Date methods such as getTime() return as Integers, which means that Date math works without the problems inherent to the Number type.    
    
### Array    
    
JavaScript does not implement a true array. Instead, it augments the Object type into an array-like object which has a length property (count of indexes) and can access members by index number.    
    
Arrays in JavaScript are also hashtable objects. This makes them very well suited to sparse array applications. When you construct an array, you do not need to declare a size. Arrays grow automatically, much like Java vectors. The values are located by a key, not by an offset. Indexes are converted to strings and used as names for retrieving values.    
    
This makes JavaScript arrays very convenient to use, but not well suited for applications in numerical analysis. Each look-up by index requires a complete hash conversion, which makes arrays the least efficient type in the language.    
    
Always prefer the literal form of array declaration (var x = [];) and remember that [all your commas are belong to array](http://wtfjs.com/2011/02/11/all-your-commas-are-belong-to-Array).    
    
```cs    
// assignment by literal is preferred    
var arr = [1, 'bob', { x: 2 }, true, null];    
// assignment by constructor is valid but not preferred    
var arr = [1, 'bob', { x: 2 }, true, null]; // valid, but d    
```    
    
Arrays are not typed, which means the value at each index can be of any type.    
    
#### Array.toString()    
    
Unless you are certain that the value of every index in an Array is a string, boolean or number: do not use the toString() method--use JSON.stringify() instead.  NOTE: You can almost never be certain, so just use JSON.stringify().    
    
```cs    
const arr = [1, 'bob', { x: 2 }, true, null];    
    
arr.toString(); // "1,bob,[object Object],true,"    
arr.toString(); // "1,bob,[object Object],true,"    
    
JSON.stringify(arr); // "[1,"bob",{"x":2},true,null]"    
JSON.stringify(arr); // "[1,"bob",{"x":2},true,null]"    
```    
    
#### Array.length    
    
Arrays, unlike objects, have a special length property. It is always 1 larger than the highest integer subscript.    
    
```cs    
const bobs = ['bob', 'robert']; // indexes 0, 1    
bobs[bobs.length] = 'rob'; // index 2    
bobs[bob.length] = 'bobby'; // index 3    
```    
    
Array.length makes it possible to iterate an array in a for(var prop in arr) loop. Do not do this. Always prefer the for(var i =0; i < arr.length; i += 1) syntax, if you are not able to use Csw methods.    
    
#### Gotchyas    
    
- Do not delete array elements using delete.    
    
```cs    
const x = [0, 1, 2, 3];    
    
x.length; // 4    
delete x[1]; // true    
x.length; // 4x //[0, undefined, 2, 3]    
    
x.splice(1, 1); // undefined    
x.length; // 3x // [0,2,3]    
```    
    
This sets the value of the element at the specified index === undefined. In almost all cases, this is not desired.    
    
- Internet Explorer < 9 does not fully implement Array. Most notably, in IE, Array's do not have an indexOf() method.    
- In the context of an array, hasOwnProperty() should be used to verify the presence of an index position--not the value at an index.    
- indexOf() as with String is used to find the position of a value.    
- IE doesn't always get length right. Beware.    
- DO NOT use the dot notation with Arrays. Always use brackets for fetch/assignment:    
    
```cs    
bobs[0]  // good    
bobs.0  //bad    
```    
    
- [Array is slower in almost all use cases than Object](http://jsperf.com/performance-of-array-vs-object). Use it only when the features of the type merit the performance hit.    
    
#### Array Methods    
    
- concat    
- every    
- filter    
- forEach    
- indexOf    
- join    
- lastIndexOf    
- map    
- pop    
- push    
- reduce    
- reduceRight    
- reverse    
- shift    
- slice    
- some    
- splice    
- toLocaleString    
- toString    
- unshift    
    
### Function    
    
Everything in JavaScript is an Object. Repeat this in a mirror three times.  Everything in JavaScript is an Object. Repeat this in a mirror three times.    
    
In JavaScript, functions are objects. Functions are first class.    
    
Objects can be passed as arguments to functions, and objects can be returned by functions.    
    
- Objects are passed by reference.    
- Objects are not passed by value.    
    
This means that functions can be passed as arguments to functions, and that functions can be returned by functions.    
    
#### Declaration    
    
The **var** statement declares and initializes a variable within a function. There is no other variable statement beyond **var**--type is not specified, it is inferred. Variables are scoped to functions: a variable declared anywhere in a function is visible everywhere in a function.    
    
All defined variables are instanced when their function scope is instanced--this is known as [hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting). Variable instancing and assignment are thus split into two steps:    
    
```cs    
// As written:    
(function () {    
  const x = 1;    
  const y = [0, 1, 2, 3];    
  for (let z = 0; z < y.length; z += 1) {    
    const a = `yuppie_${z}`;    
  }    
})();    
// As executed:    
(function () {    
  let x;    
  let y;    
  let z;    
  let a;    
  x = 1;    
  y = [0, 1, 2, 3];    
  for (z = 0; z < y.length; z += 1) {    
    a = `yuppie_${z}`;    
  } // Observe that the value of 'a' will always be the value of 'yuppie_' + y.length-1. This is critical to understanding closure.    
})();    
```    
    
Functions may be declared explicitly as variables or implicitly using shorthand, but they are compiled and interpreted identically.    
    
```cs    
// As written    
function MyFoo(id, value) {    
  return id === value;    
}    
// As executed    
var MyFoo = undefined;    
MyFoo = function (id, value) {    
  return id === value;    
};    
```    
    
#### Return    
    
By design, all functions return a value. If no **return** statement is specified, the return value is undefined. With the use of **return,**any type of Object may be returned from a function.    
    
The only exception to this rule is within functions called as constructors, that is called with the **new** prefix. When calling a function with new, such as in **new** Date(), the return is a new instance of the prototype of the specified object.    
    
With few exceptions, we never need to do this in JavaScript.    
    
Exceptions:    
    
```cs    
var myDate =  new  Date('01/20/2011');    
...    
throw  new  Error('unhandled exception');    
throw  new  TypeError('expected a type of function');    
```    
    
#### Inheritance    
    
JavaScript does not implement a classical inheritance model, it uses a prototypical one. A prototype based model can be extended to support a classical model, but the inverse is not true. While you can bend JavaScript to look like a classic model, it is almost always more efficient, readable and maintainable to work with the functional model.    
    
##### Pseudo-classical style    
    
```cs    
function Gizmo(id) {    
  this.id = id;    
}    
Gizmo.prototype.toString = function () {    
  return `gizmo ${this.id}`;    
};    
function Hoozit(id) {    
  this.id = id;    
}    
Hoozit.prototype = new Gizmo();    
Hoozit.prototype.test = function (id) {    
  return this.id === id;    
};    
```    
    
##### Functional Style    
    
```cs    
function gizmo(id) {    
  return {    
    id,    
    toString() {    
      return `gizmo ${this.id}`;    
    },    
  };    
}    
function hoozit(id) {    
  const that = gizmo(id);    
  that.test = function (testid) {    
    return testid === this.id;    
  };    
  return that;    
}    
```    
    
The functional approach delivers the exact same result as the pseudo-classical approach, but it is cleaner and easier to understand.    
    
##### Closure    
    
Closure is one of the best parts of the language. When a function is instanced--assigned to a variable, it maintains the private variables which were declared within it. These variables are hidden from the global object and indeed from every other object in the same scope. Without closure, we write code like this:    
    
```cs    
// Globally scoped    
const names = ['zero', 'one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine'];    
const digit_name = function (n) {    
  return names[n];    
};    
    
alert(digit_name(3)); // 'three'    
```    
    
- This is problematic, because names is exposed to the global object. It can be modified by any other object at any time.    
    
Slightly better, using function scope to protect names:    
    
```cs    
const digit_name = function (n) {    
  const names = ['zero', 'one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine'];    
  return names[n];    
};    
    
alert(digit_name(3)); // 'three'    
```    
    
- This is better. Names is protected and no one outside of digit_name can modify the names array; however, this is extremely slow. Names is redefined on every invocation of the function. In almost all practical use cases, the result will be slow execution.    
    
Much better, using Closure:    
    
```cs    
const digit_name = (function () {    
  const names = ['zero', 'one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine'];    
  return function (n) {    
    return names[n];    
  };    
})();    
    
alert(digit_name(3)); // 'three'    
```    
    
- This is much better. Names is still protected. This is also much faster. We are assigning the instance of an anonymous function which returns the function we need to perform the lookup. As long as digit_names remains in scope, it will persist a private closure with local, in-memory access to the names collection. Names is instanced only once and persists for the life of digit_names. Our behavior is fast and our variable is protected.    
    
##### Security    
    
One of the nice features of closure is the ability to securely create, seal and open objects. In JavaScript, this kind of functionality is trivial to create:    
    
```cs    
function makeBoxes() {    
  const boxes = [];    
  const values = [];    
  return {    
    sealer(value) {    
      let box;    
      const i = boxes.length;    
      box = {};    
      boxes[i] = box;    
      values[i] = value;    
      return box;    
    },    
    unsealer(box) {    
      return values[boxes.indexOf(box)];    
    },    
  };    
}    
```    
    
#### Recursion    
    
Bells and Kazoos    
    
### [Y Combinator](http://en.wikipedia.org/wiki/Fixed_point_combinator#Y_combinator)    
    
```cs    
function y(le) {    
  return (function (f) {    
    return f(f);    
  })(function (f) {    
    return le(function (x) {    
      return f(f)(x);    
    });    
  });    
}    
const factorial = y(function (fac) {    
  return function (n) {    
    return n <= 2 ? n : n * fac(n - 1);    
  };    
});    
const number120 = factorial(5);    
```    
    
#### Function.toString()    
    
Function implements toString(). Don't use it.    
    
#### Debugging    
    
### Console    
    
ECMAScript does not specify a standard **console** object, but all modern browsers implement **console**. IE 8 included the last updates to the console object, carrying bugs in its support into IE 9. Firefox, Chrome, Opera and Safari keep feature parity on console through iteration: almost all changes to console by one vendor are adopted by the others in the next iteration.    
    
Console is accessible in the DOM, which means that console calls can be placed inside JavaScript and then viewed in the actual browser console (ABC) at a later time. Console is also accessible outside the DOM, which means that console methods can be called in the ABC at any time.    
    
### Logging    
    
- log(context, arg1, arg2, n): Write an object to the console. log can take an arbitrary number of parameters (which it will concatenate together), and it supports inline parameter definition in the style of printf:    
  - `console.log("This function is %s %s.","foo","bar");//This function is foo bar.`    
  | -     | Pattern | Type |
  | ----- | ------- |
  | %s    | String  |
  | %d %i | Number  |
  | %o    | Object  |
    
- debug(context, arg1, arg2, n): console.log an object to the console with a link to the line number of execution.    
  - In Chrome, log === debug === info    
- info(context, arg1, arg2, n): console.log an object to the console with a styling of info. (Blue circle with 'i').    
  - In Chrome, log === debug === info    
- warn(context, arg1, arg2, n): console.log an object to the console with a styling of warning. (Yellow triangle with '!').    
- error(context, arg1, arg2, n): console.log an object to the console with a styling of error. (Red circle with 'x').    
- assert(true === false): Output a truthy evaluation to the console if the assertion is false with a styling of error.    
    
### Profiling    
    
- time: used to capture the start and end time for a particular identifier    
    
```cs    
console.time("Beginning Async Call #1");  //undefined    
...    
console.timeEnd("Beginning Async Call #1");  //Beginning Async Call #1: 6319ms    
```    
    
- time(identifier) defines a string name to mark for a later timeEnd(identifier) to match against.    
  - Multiple instances of console.time()..timeEnd() may be defined within the same function scope as long as each identifier is unique.    
- profile: used to capture verbose performance information for a particular identifier    
    
```cs    
console.profile("Begin Async Call #2");    
...    
console.profileEnd("Begin Async Call #2");    
```    
    
- profile(identifier) defines a string name to mark for a later profileEnd(identifier) to match against.    
  - profile behaves in the same manner as time, except the output will be more verbose.    
  - profile verbose output appears in the "Profiles" tab of the console window.    
- timeStamp(indentifier): Used to output a timestamp with a string identifier.    
  - markTimeline(): Parallel implementation    
- profiles: An array containing all currently defined profiles on the console object    
- memory: An object defining current memory consumption in the DOM    
- count(identifier): Outputs a count of the number of times identifier was called to the log. Useful for evaluating redundant method calls.    
    
### Grouping    
    
Console operations can be grouped into a single, collapsible console output: useful for outputting data across methods, modules and files and grouping the output together.    
    
- group: defines a logical grouping for a particular identifier    
    
```cs    
console.group("Async Calls #1");    
...    
console.groupEnd("Async Calls #1");    
```    
    
- group(identifier) defines the beginning of a logical collection which groupEnd(identifier) terminates.    
- groupCollapsed(identifier): outputs a group by identifier to the log.    
    
### Output    
    
- trace(): Outputs the call stack (at the moment trace is called) to the console    
- dir(element): Outputs an interactive representation of a DOM object with all of its properties to the console.    
- dirxml(element): Outputs an XHTML representation of a DOM object to the console.    
    
### Resources    
    
- [Chrome Dev Tools: Console](http://code.google.com/chrome/devtools/docs/console.html)    
    
**Your attempts to verify this claim may vary. Please update when an authoritative answer is found.    
