---
author: CF
categories:
  - promises
  - code
  - persistence
comments: true
description: "Quite a bit of my free time in the last 8 months has slipped away into OJ..."
draft: false
image: images/2013-06-26-a-promise-to-indexed-db.jpeg
layout: post
title: A Promise to IndexedDb
toc: false
---
    
Quite a bit of my free time in the last 8 months has slipped away into [OJ](http://somecallmechief.github.io/oj/), my solution to the hell of dynamic form creation (based on arbitrary data inputs and accordingly arbitrary data outputs). Folks at [WuFoo](http://www.wufoo.com/) and [Bootsnip](https://bootsnipp.com/index.php/forms) have some nice prototypes of runtime manipulatable engines; while each looks like excellent work, neither solves my problem of generating entire workflows based on data. I want the data to drive the UI; the UI should simply read the data and generate the necessary rich form content necessary to provide slick, intuitive, addictive user experiences.    
    
In the pursuit of this heffalump, I had recently been working on decomposing [Martin Orth](http://www.youtube.com/user/karnevalCom)'s [query builder](http://www.youtube.com/watch?v=QJwlEry_ER4) into classes in the OJ framework (see it on [Github](https://github.com/somecallmechief/oj), in [NPM](https://npmjs.org/package/ojs), and collaborate with me in [Cloud9](https://c9.io/somecallmechief/oj)). This body of work depends upon [ExtJs](http://www.sencha.com/products/extjs), which is a powerful but extraordinarily inscrutable framework. I spend a frustrating amount of time comparing the inputs into Ext from OJ vs the inputs in Sencha's examples or their documentation. It's tedious and fragile as seeming minor changes can produce difficult to track bugs which go unobserved across several commits.    
    
I wanted to be able to instrument my code in such a way that I could flip a switch and start logging every input into Ext. I had a few requirements: the objects logged needed to be in as close a state as possible to their values at the time of logging (logging to the console sends the object by reference, so you'll usually get the last known value of the object by the time you see it). The objects needed to persist across page loads. There would be a lot of data (more than would fit in localStorage). Finally, the data needed to be queryable.    
    
[IndexedDb](https://developer.mozilla.org/en-US/docs/IndexedDB%E2%80%8E) seemed like a potentially good fit. Unfortunately, like so many "HTML5" standards, [the API is terrible](https://developer.mozilla.org/en-US/docs/IndexedDB). If the consumers of this API were just vendors, like Chrome and Firefox, it would be hard to identify any specific contract as "wrong"; but as a developer consuming this API, I cannot easily point to any component and say, "this is right." But it is what it is, and it is all we've got.    
    
So in an effort to transmogrify the indexedDb API into something that I would actually want to use myself, I put together a proof-of-concept wrapped around [Promises](http://wiki.commonjs.org/wiki/Promises/A). I am developing it inside of OJ, but it could easily be factored out to use independently.    
    
You'll need:    
    
- [Q.js](https://github.com/kriskowal/q) ([Kris Kowal](http://www.blogger.com/profile/01443956999129365941)'s excellent Promise library)    
- The [IndexedDb polyfill](http://nparashuram.com/IndexedDBShim/) (to support the IEs of the world, and such)    
- [FakerJs](https://github.com/marak/Faker.js/%E2%80%8E) (Optional: very useful to get prototypes running without worrying about creating the data)    
- [OJ](http://somecallmechief.github.io/oj/) (Optional: only if you don't want to have to factor out my helper methods)    
    
This is the API that I wanted to call:    
    
```js    
var tableName = 'messages',    
  dbName = 'diagnostics',    
  dbVersion = 1;    
    
//Create a new DB manager instance    
var newDbMgr = initDb();    
//Connect to a specific database    
newDbMgr.connect(dbName, dbVersion);    
    
//Create or extend a schema    
//newDbMgr.ddl.dropTable(tableName);    
newDbMgr.ddl.createTable(tableName, 'messageid', true); //true == auto manage primary key    
newDbMgr.ddl.createIndex(tableName, 'subjectid', 'text.subject');    
newDbMgr.ddl.createIndex(tableName, 'timeid', 'time');    
newDbMgr.ddl.createIndex(tableName, 'usernameid', 'user.name');    
    
//Insert some data    
newDbMgr.insert(tableName, {    
  message: {    
    time: new Date(),    
    text: {    
      fault: Faker.Lorem.words(),    
      subject: Faker.Lorem.sentence(),    
      description: Faker.Lorem.paragraph(),    
    },    
    user: Faker.Helpers.createCard(),    
  },    
});    
newDbMgr.insert(tableName, {    
  message: {    
    time: new Date(),    
    text: {    
      fault: Faker.Lorem.words(),    
      subject: Faker.Lorem.sentence(),    
      description: Faker.Lorem.paragraph(),    
    },    
    user: Faker.Helpers.createCard(),    
  },    
});    
    
//Query the data    
var results = newDbMgr.select('message').from(tableName).where('user.name', '=', 'Bob');    
```    
    
To make it happen, some work needed to be done. First, you need a database connection. I never liked the implementation of "versioning" the database in WebSQL--but conceptually, it is the same mystery bag of disappointments in iDb; so the first step is to abstract and encapsulate the mechanism for opening a database connection and handling the version control events.    
    
```js    
var initDb = (function() {    
 var name;    
 //Store the last used db name, db version and db connect promise in the closure    
 var version, db, connectPromise;    
 var reInit = false;    
    
 //The actual connect method to be used in order to establish a connection and/or trigger versioning    
 var connect = function(dbName, dbVersion, dbOnUpgrade) {    
  //Only create a new promise if connecting to a different db name or version; otherwise, the last issued promise is still good.    
  reInit = (!connectPromise || dbName !== name || dbVersion !== version);    
  if(reInit) {    
   //Create a new promise from scratch    
   var deferred = Q.defer();    
    
   //Cache our state data in the outer closure    
   connectPromise = deferred.promise;    
   version = dbVersion || 1;    
   name = dbName;    
   dbOnUpgrade = dbOnUpgrade || function() {};    
    
   //Create the connection request. This is an async operation, which the promise will resolve.    
   var request = window.indexedDB.open(name, version);    
    
   //Optionally do something when the request is completed (with or without error)    
   request.oncomplete = function () {    
    
            }    
    
   //Someone or something has explicitly aborted the request    
            request.onabort = function () {    
                window.console.error('Connection attempt to ' + name + ' was aborted. Closing db connection now.');    
                db.close();    
            }    
    
   //Connection has timed out    
            request.ontimeout = function () {    
    window.console.error('Connection attempt to ' + name + ' timed out. Closing db connection now.');    
                db.close();    
            }    
    
   //Another process has a lock on the database    
   request.onblocked = function(event) {    
    //Resolve the promise with rejection    
    deferred.reject(new Error("Database error: " + event.target.errorCode));    
    window.console.error('Connection attempt to ' + name + ' was blocked by another process. Is the db open in another window/tab? Did you forget to close your connection to the db before attempting to version it? Closing db connection now.');    
    db.close();    
   };    
    
   //An error occurred at any time during the lifecycle of the request.    
   request.onerror = function(event) {    
    //Resolve the promise with rejection    
    deferred.reject(new Error("Database error: " + event.target.errorCode));    
    window.console.error('Connection to ' + name + ' failed with error: ' + event.target.errorCode + '. Closing db connection now.');    
    if(db) {    
     db.close();    
    }    
   };    
    
   //Versioning happens before success (promise is not yet resolved, but we have a handle on the db instance)    
   request.onupgradeneeded = function(event) {    
    db = event.target.result;    
    
    dbOnUpgrade(db);    
   };    
    
   //Request has succeeded; versioning may or may not have happened; connection to the database is established    
   request.onsuccess = function(event) {    
    db = request.result;    
    //Resolve the promise with success and the db instance    
    deferred.resolve(db);    
   };    
  }    
    
  //Return either the new promise or the existing    
  return connectPromise;    
 };    
    
 //More code to follow    
 ....    
}());    
```    
    
Hopefully the commentary in the code is sufficient to communicate its meaning. In essence, I'm leveraging Q's deferred promise wrapper against the resolution of the iDb request's async callbacks. Of course, if indexedDb simply implemented its many cascading callbacks as promises, this post might not exist.    
    
Once the connection wrapper is defined, I can encapsulate some of the DDL operations into this new API.    
    
```js    
//private implementation method    
var createTableImpl = function (dbWrapper, db, tableName, tablePkColumnName, autoIncrement) {    
  //The call to createObjectStore is synchronous--the table is immediately returned    
  var table = db.createObjectStore(tableName, {    
    keyPath: tablePkColumnName,    
    autoIncrement: false !== autoIncrement,    
  });    
  //Cache the table instance for future reference    
  dbWrapper.schema.add(tableName, table);    
  return table;    
};    
    
//public method. DDL operations can only happen as part of the versioning event.    
//Add the call to create the table to a schema scripts collection which the IndexedDb versioner will iterate.    
var createTable = function (dbWrapper, tableName, tablePkColumnName, autoIncrement) {    
  //Create a new promise    
  var deferred = Q.defer();    
  //Push this method into the scripts collection    
  schemaScripts.push(function (db) {    
    try {    
      //Create the table    
      var objectStore = createTableImpl(dbWrapper, db, tableName, tablePkColumnName, autoIncrement);    
      //Resolve the promise successful with the table    
      deferred.resolve(objectStore);    
    } catch (e) {    
      console.log(e, e.stack);    
      //Resolve the promise failed    
      deferred.reject(new Error('Could not create a new table', e));    
    }    
    return dbWrapper.schemaschemaschemaschema\[tableNametableNametableNametableName\];    
  });    
  return deferred.promise;    
};    
```    
    
If you return to my dream API above, you'll notice that I placed the DDL operations first. Because this implementation uses promises, the order of operations is extraordinarily flexible. I could just as easily position the calls to DDL operations after a long list of calls to DML operations, and the Q promise layer would just make it work. This is in stark contrast to the iDb event model which is highly dependent upon the execution of functions being in the right place at the right time. Promises allow me, as a developer, to think less about the coordination of sequencing to/from other APIs and more about the order I want my own code to execute.    
    
At any rate, the above is just enough to create some indexedDb tables. Next, the core of indexedDb: [indexes](http://grammarist.com/usage/indexes-indices/).    
    
```js    
//Private implementation method    
var createIndexImpl = function (dbWrapper, tableName, columnName, indexName, isUnique) {    
  //No need to wait on transaction, we have (or should have) the table in memory    
  var table = dbWrapper.schemaschemaschemaschema\[tableNametableNametableNametableName\];    
  //Create the new index and return it immediately (happens synchronously)    
  //It would be nice to cache the index on the cached table instance, but that table instance is actually an indexedDb object and I don't want to mutate--nor do I want to refactor anything at the moment. So eat the cost of fetching a handle on indexes later. Told-you-so's expected.    
  return table.createIndex(columnName, indexName || columnName + 'Idx', {    
    unique: true !== isUnique,    
  });    
};    
    
//public method. DDL operations can only happen as part of the versioning event.    
//Add the call to create the index to a schema scripts collection which the IndexedDb versioner will iterate.    
var createIndex = function (dbWrapper, tableName, columnName, indexName, isUnique) {    
  //Create a new promise    
  var deferred = Q.defer();    
  //Push this method into the scripts collection    
  schemaScripts.push(function () {    
    try {    
      var index = createIndexImpl(dbWrapper, tableName, columnName, indexName, isUnique);    
      //Resolve the promise successful with the new index    
      deferred.resolve(index);    
    } catch (e) {    
      console.log(e, e.stack);    
      //Fail the promise    
      deferred.reject(new Error('Could not create a new index', e));    
    }    
    return dbWrapper.schemaschemaschemaschema\[tableNametableNametableNametableName\];    
  });    
  return deferred.promise;    
};    
```    
    
Very much like the createTable method before it, the createIndex method hooks into the schema scripts collection and executes on the db versioning event. Unlike WebSQL or SQLite, IndexedDb is designed to be a non-necessarily-relational _object_ store. So it stores _objects_. Objects can be primitive values, arrays or complex Object instances with deeply nested, non-relational tree structures. Indexes are levied against the property names of the values you want to be indexed.    
    
```js    
var obj = {    
  str: '',    
  num: 0,    
  arr: \[\],    
  obj1: {    
    str1: '',    
    obj2: {    
      str2: '',    
    },    
  },    
};    
```    
    
So an index on "str2" is as simple as defining an index on "obj1.obj2.str2". This took me quite a bit of trial and error, so I think it's worth highlighting how the pathing to an index works.    
    
Finally (for the purpose of this post), you need a way to insert data into the store.    
    
```js    
//Private implementation method, doesn't yet conform to standard \*Impl paradigm. It's ok; I'm ok; you're ok.    
var insertImpl = function (tableName, records) {    
  //Promise to insert the data    
  var deferred = Q.defer();    
    
  try {    
    //Get a new transaction on the table. This is an insert, so 'readwrite' is implicitly understood by the caller.    
    var transaction = db.transaction(\[tableNametableNametableNametableName\], 'readwrite');    
    
    //Get the object store from the transaction (gods forbid we fetched it from our own handle on the object store...and how the hell does this thing manage concurrency?!)    
    var objectStore = transaction.objectStore(tableName);    
    //Insert the new records    
    n$.each(records, function (rec) {    
      objectStore.add(rec);    
    });    
    //Resolve the promise    
    deferred.resolve(true);    
  } catch (e) {    
    console.log(e, e.stack);    
    //Fail the promise    
    deferred.reject(new Error('Could not insert records', e));    
  }    
  //Return the promise    
  return deferred.promise;    
};    
    
//Public insert method    
var insert = function (tableName, records) {    
  var ret = function () {    
    return insertImpl(tableName, records);    
  };    
  //If we have a db instance or if the initial connection promise is resolved (succeeded), then it is safe to insert immediately    
  if (db || connectPromise.isResolved()) {    
    ret();    
  } else {    
    //else wait for the connection promise to resolve and then do the insert (this seems to be the normal use case)    
    connectPromise.then(ret);    
  }    
  //Nothing worth returning at this time    
  //return ret.promise;    
};    
```    
    
I still have to put the finishing touches on some more of this API. Transactions become a bigger deal as you begin to write more complicated queries, and indexes (and their cursors) play a much, much larger role than I have touched upon here; but I think that this provides one alternative to the problem of working with indexedDb that is simple.    
    
This whole abstraction needs to be lazy, and there is another round of refactoring I need to do to move it into a pure functional style; but I think it is more readable and approachable this way.    
    
I hope to have JsPerf tests and an expanded API for my next post. Until then, I'll leave you with the full example code:    
    
```js    
var initDb = (function () {    
  var name,    
    version,    
    db,    
    connectPromise,    
    upgradeIsRequired = false;    
  var schemaScripts = \[\];    
    
  var connect = function (dbName, dbVersion, dbOnUpgrade) {    
    upgradeIsRequired = !connectPromise || dbName !== name || dbVersion !== version;    
    if (upgradeIsRequired) {    
      var deferred = Q.defer();    
    
      connectPromise = deferred.promise;    
    
      version = dbVersion || 1;    
      name = dbName;    
      dbOnUpgrade = dbOnUpgrade || function () {};    
    
      var request = window.indexedDB.open(name, version);    
    
      request.onblocked = function (event) {    
        db.close();    
        alert('A new version of this page is ready. Please reload!');    
      };    
    
      request.onerror = function (event) {    
        deferred.reject(new Error('Database error: ' + event.target.errorCode));    
        if (db) {    
          db.close();    
        }    
      };    
      request.onsuccess = function (event) {    
        db = request.result;    
        deferred.resolve(db);    
      };    
      request.onupgradeneeded = function (event) {    
        db = event.target.result;    
    
        if (schemaScripts.length > 0) {    
          n$.each(schemaScripts, function (script) {    
            //debugger;    
            script(db);    
          });    
        }    
    
        dbOnUpgrade(db);    
      };    
    }    
    return connectPromise;    
  };    
    
  var disconnect = function () {    
    if (connectPromise.isFulfilled()) {    
      db.close();    
    } else if (db) {    
      connectPromise.done(db.close);    
    }    
  };    
    
  var createTableImpl = function (dbWrapper, db, tableName, tablePkColumnName, autoIncrement) {    
    var table = db.createObjectStore(tableName, {    
      keyPath: tablePkColumnName,    
      autoIncrement: false !== autoIncrement,    
    });    
    dbWrapper.schema.add(tableName, table);    
    return table;    
  };    
    
  var createTable = function (dbWrapper, tableName, tablePkColumnName, autoIncrement) {    
    var deferred = Q.defer();    
    schemaScripts.push(function (db) {    
      try {    
        var objectStore = createTableImpl(    
          dbWrapper,    
          db,    
          tableName,    
          tablePkColumnName,    
          autoIncrement,    
        );    
        deferred.resolve(objectStore);    
      } catch (e) {    
        console.log(e, e.stack);    
        deferred.reject(new Error('Could not create a new table', e));    
      }    
      return dbWrapper.schemaschemaschemaschema\[tableNametableNametableNametableName\];    
    });    
    return deferred.promise;    
  };    
    
  var createIndexImpl = function (dbWrapper, tableName, columnName, indexName, isUnique) {    
    var table = dbWrapper.schemaschemaschemaschema\[tableNametableNametableNametableName\];    
    return table.createIndex(columnName, indexName || columnName + 'Idx', {    
      unique: true !== isUnique,    
    });    
  };    
    
  var createIndex = function (dbWrapper, tableName, columnName, indexName, isUnique) {    
    var deferred = Q.defer();    
    
    schemaScripts.push(function () {    
      try {    
        var index = createIndexImpl(dbWrapper, tableName, columnName, indexName, isUnique);    
        deferred.resolve(index);    
      } catch (e) {    
        console.log(e, e.stack);    
        deferred.reject(new Error('Could not create a new index', e));    
      }    
      return dbWrapper.schemaschemaschemaschema\[tableNametableNametableNametableName\];    
    });    
    return deferred.promise;    
  };    
    
  var insertImpl = function (tableName, records) {    
    var deferred = Q.defer();    
    
    try {    
      var transaction = db.transaction(\[tableNametableNametableNametableName\], 'readwrite');    
    
      var objectStore = transaction.objectStore(tableName);    
      n$.each(records, function (rec) {    
        objectStore.add(rec);    
      });    
    
      deferred.resolve(true);    
    } catch (e) {    
      console.log(e, e.stack);    
      deferred.reject(new Error('Could not insert records', e));    
    }    
    
    return deferred.promise;    
  };    
    
  var insert = function (tableName, records) {    
    var ret = function () {    
      return insertImpl(tableName, records);    
    };    
    if (db || connectPromise.isResolved()) {    
      ret();    
    } else {    
      connectPromise.then(ret);    
    }    
    //return ret.promise;    
  };    
    
  return function () {    
    var ret = n$.object();    
    ret.add('connect', connect);    
    ret.add('disconnect', disconnect);    
    ret.add('schema', n$.object());    
    ret.add('ddl', {    
      createTable: function (tableName, tablePkColumnName, autoIncrement) {    
        var args = Array.prototype.slice.call(arguments, 0);    
        args.unshift(ret);    
        return createTable.apply(this, args);    
      },    
      dropTable: function (tableName) {    
        var args = Array.prototype.slice.call(arguments, 0);    
        args.unshift(ret);    
        return createTable.apply(this, args);    
      },    
      createIndex: function (tableName, columnName, indexName, isUnique) {    
        var args = Array.prototype.slice.call(arguments, 0);    
        args.unshift(ret);    
        return createIndex.apply(this, args);    
      },    
    });    
    ret.add('insert', insert);    
    return ret;    
  };    
})();    
```    
