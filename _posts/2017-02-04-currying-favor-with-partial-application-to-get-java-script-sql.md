---
author: CF
categories:
  - currying
  - code
  - partial
  - application
comments: true
description: >-
  Editor’s Note This is an old post from March of 2013 from an old blog
  Apparently it still gets enoug...
draft: false
image: >-
  images/2017-02-04-currying-favor-with-partial-application-to-get-java-script-sql.jpeg
layout: post
title: Currying Favor with Partial Application to get JavaScript SQL
toc: false
---
    
_Editor’s Note: This is an old post from March of 2013, from an old blog. Apparently it still gets enough hits to need updating as links have changed. Re-posting it here with URL corrections._    
    
There is a domain that attends to curry as one of the quintessential elements of regional cuisine. This is not that domain; though in this domain I would argue we need a paprika.    
    
If the terms _curry_ or _partial application_ are at all unfamiliar to you, I highly recommend Reg Braithwaite’s [latest opus on the subject](http://raganwald.com/2013/03/07/currying-and-partial-application.html). Lending support are [+Ben Alman](http://plus.google.com/112487099551149077731) with his own [eviscerating tour de force](http://benalman.com/news/2012/09/partial-application-in-javascript/) as well as [+Axel Rauschmayer](http://plus.google.com/110516491705475800224) with his [very, extremely, routinely reputable entry](http://www.2ality.com/2011/09/currying-vs-part-eval.html). If you’ve ever followed me here before, you may remember a fog surrounding an idea that touched on partial application from [Good Reads](http://hiking.luddites.me/2013/01/good-reads.html), where I linked to (again to Mr. Braithwaite) [the skinny](https://github.com/raganwald/homoiconic/blob/master/2013/01/practical-applications-of-partial-application.md).    
    
Do not be confused, dismayed, disheartened or discouraged. Currying and partial application are not easy-to-grok. The literature on the subject, while vast, is dense. Even in the most skilled hands, attempts to bring these tomes down from Mt. Sinai, have not routinely resulted in greater clarity.    
    
But this is not yet-another-indoctrination on the subject. Defer to the authorities above for that. Here, I’m attempting to exploit the potential for good. Using both ingredients, it is possible to create a semantic for querying JavaScript objects in a syntax that resembles SQL. Are there other solutions to do this? [Yes](https://plus.google.com/108988276571177665337/posts/ahm5G625Vix).    
    
Once you’ve apprehended grokation of the concepts, it should be straightforward to see the implementation of a few partial application standard methods: [map](http://en.wikipedia.org/wiki/Map_%28higher-order_function%29), [filter](http://en.wikipedia.org/wiki/Filter_%28higher-order_function%29) and [fold](http://en.wikipedia.org/wiki/Fold_%28higher-order_function%29). Lots of libraries have already done the diligence and written these for us but for the sake of having something to write, let’s implement them again (In the real world, you’re better off taking what [functional js](http://wiht.link/FunctionalJS) or [wu.js](http://fitzgen.github.com/wu.js) have already built)!    
    
function curryLeft(func) {    
var slice = Array.prototype.slice;    
var args = slice.call(arguments, 1);    
return function() {    
return func.apply(this, args.concat(slice.call(arguments, 0)));    
    
}    
}    
    
function foldLeft(func,newArray,oldArray) {    
var accumulation = newArray;    
each(oldArray, function(val) {    
accumulation = func(accumulation, val);    
});    
return accumulation;    
    
}    
    
function map(func, array) {    
var onIteration = function(accumulation, val) {    
return accumulation.concat(func(val));    
};    
return foldLeft(onIteration, \[\], array)    
}    
    
function filter(func, array) {    
var onIteration = function(accumulation, val) {    
if(func(val)) {    
return accumulation.concat(val);    
} else {    
return accumulation;    
}    
};    
return foldLeft(onIteration, \[\], array)    
}    
    
With just these, we can do something that’s almost cool. We can extend the native Array class to add some new methods:    
    
Object.defineProperties(Array.prototype, {    
'\_where': {    
value: function(func) {    
return filter(func, this);    
}    
},    
'\_select': {    
value: function(func) {    
return map(func, this);    
}    
}    
});    
    
At this point, given an instance of an Array (thanks to [Faker](https://github.com/marak/Faker.js/)), like:    
    
var somePeople = \[    
{"FirstName":"Cristina", "LastName":"Quigley", "PhoneNumber":"1-189-868-2830", "Email":"Imelda@lourdes.ca", "Id":0},    
{"FirstName":"Eriberto", "LastName":"Bailey", "PhoneNumber":"1-749-549-2050 x36612", "Email":"Pamela\_Gaylord@ludie.net", "Id":1},    
{"FirstName":"Amina", "LastName":"Schaden", "PhoneNumber":"463-301-9579 x9511", "Email":"Conner\_Gusikowski@jolie.tv", "Id":2}\];    
    
If we wanted to select the FirstName of each record, we could do something crude like:    
    
somePeople.\_select(function(row) { return row.FirstName });    
    
But this is still too obtuse. With a little curry, we can make it better. We used partial application to get to ‘\_select’, but we can switch gears to curry to get a better query mechanism. First, let’s define a query method: _Update: we technically don’t need curry here, as we’re not abstracting the ‘query’ object as a parameter. Thanks to Thomas Burette in the comments_.    
    
var query = function(array) {    
var tables = \[\];    
tables.push(array);    
var \_query = {    
tables: tables,    
from: from,    
select: select,    
run: run    
};    
return \_query;    
};    
    
select and from methods are straightforward:    
    
function select() {    
var query = this;    
    
    var slice = Array.prototype.slice;    
    var args = slice.call(arguments, 0);    
    query.columns = query.columns || \[\];    
    each(args, function(argumentValue) {    
        query.columns.push(argumentValue);    
    });    
    return query;    
    
}    
    
function from(array) {    
var query = this;    
query.tables.push(array);    
return query;    
}    
    
which then only leaves execution. I’ve deliberately not optimized this method for the purpose of illustration: in the absence of the tools, this is what such code looks like. Look at the redundancy and duplication. Marvel at the inelegance. [Appreciate the fact that it works](http://prog21.dadgum.com/169.html).    
    
function run() {    
var query = this;    
var ret = \[\];    
if (query.columns.length > 0) {    
var results = \[\];    
each(query.columns, function(columnName) {    
    
            each(query.tables, function(tbl) {    
                if (Array.isArray(tbl)) {    
                    var res = {};    
                    var val = tbl.\_select(function(val) {    
                        return val\[columnName\];    
                    });    
                    if (val) {    
                        res\[columnName\] = val;    
                        results.push(res);    
                    }    
                }    
            }, true);    
    
        });    
    
        var returnRows = \[\];    
        if(results && results.length > 0) {    
            var firstResult = results\[0\];    
    
            each(firstResult, function(val, key) {    
    
                each(val, function(cell){    
                    var row = {};    
                    row\[key\] = cell;    
                    each(results.slice(1), function(result) {    
                        each(result, function(v,k){    
                            each(v, function(c) {    
                                row\[k\] = c;    
                            })    
                        },true)    
                    },true)    
                    returnRows.push(row);    
                },true);    
    
            },true)    
    
        }    
    
    }    
    return returnRows;    
    
}    
    
Now, this yields a syntax which looks a lot more like SQL:    
    
var newQuery = query(people).select('FirstName', 'LastName');    
var results = newQuery.run();    
    
From here, ‘where’, ‘join’ (yes I said JOIN), ‘orderby’ and ‘groupby’ are all implementation details. This is just a proof-of-concept post, but given some large Faker data sets it already works quite well given its limitations. Refactoring ‘run’ into a method which utilizes partial application will yield mountains.    
    
As always, everything I blog and code is public domain. You can view the source from the [oj-sql project here](https://github.com/somecallmechief/oj-sql), collab with me on [c9 here](https://c9.io/somecallmechief/oj-sql), or do whatever strikes your whim. May the wind that strikes your whim be always at your back.    
