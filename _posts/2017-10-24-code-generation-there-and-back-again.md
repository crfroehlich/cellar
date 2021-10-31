---
author: CF
categories:
  - metadata
  - retrospective
  - code
  - generation
comments: true
description: >-
  I can’t remember a project engineering or otherwise during which I haven’t
  paused to ask myself “Why...
draft: false
image: images/2017-10-24-code-generation-there-and-back-again.jpg
layout: post
subtitle: ' There and Back Again'
title: 'Code Generation: There and Back Again'
toc: true
---
    
I can’t remember a project (engineering or otherwise) during which I haven’t paused to ask myself: “Why did I design this thing that way?” Sometimes as a result of this introspection, I find myself feeling remorse or self-loathing, regret or the hollow embrace of ennui. Sometimes the buyer’s remorse can begin to settle in only after years of work. Sometimes the anthropomorphic representation of the design conceit simply lurks in the shadows sewing mischief as it’s able. I’ll defer to future versions of myself to conclude the relative merits or lack thereof of this particular set of concerns.    
    
Every application — from this humble blog to the social behemoths starts with data. Most projects start with a fairly predictable set of questions: what language(s) are we going to write this thing in, what type of application is this, and where are we going to store the data? Vast amounts of our data is purely relational; equal volumes of equally critical data isn’t. At the end of the day, a relational database seemed the right choice — and most grown up databases these days support storing and searching on structured and unstructured data.    
    
I think this choice continues to make sense. Personally, I’ve always found managing relational databases at scale at the lower end of the spectrum of things which bring me joy as a developer. Data migrations, schema changes, wrestling with referential integrity constraints as customer requirements change…the whole mess of managing data leaves a foul taste that’s difficult to rinse out. So a younger version of myself, a rather clever fellow in his own mind, came up with a solution:    
    
Code generation!    
    
Looking back to the earliest commits in our repo, some of the very first were the JSON files that form the backbone of our code generating homunculus. A few simple properties in our library of JSON files are responsible for generating every C# class that backs the tables in our database. Tired of a property? Update a JSON file. Need more tables, more properties, more relationships? It’s all just JSON.    
    
This makes other aspects of developing the architecture rather pleasant. Need to create or maintain a factory? Never write another line of factory code by hand. Need certain classes to implement an interface? Automate it. Need DTO versions of your table classes? Already done. RESTful web services that automatically implement ALL the verbs for every class and every possible relationship between entities? Our homunculus did that for you the day before you asked for it.    
    
Orthogonally, at the database tier, we made a rather nice design choice to use globally unique ids. Our first table committed to code is a simple dictionary of Ids and Types. Every other table inherits from this base table, which means that given any valid Id, I can find the corresponding dog, cat, person or object with no extra information. When it comes to representing these data structures as REST resources, our routes can be as terse or verbose as desired. If we were so inclined, the route to add a dog to a person’s pets collection could be as simple as posting to _/api/{personId}/{petId}_, although in practice we rarely deviate from RESTful norms (_/api/person/{personId}/pets/{petId}_ is the actual route).    
    
The flexibility that working with globally unique identifiers would be half as pleasant if we could not automatically create and maintain the factories required to fetch an object by id and then cast it to the appropriate implementation class. As objects mature, they often have a tendency to develop greater and deeper relationships with other objects in the database. Automatically generating these relationships and the pathways between them at every tier of our framework has been a boon.    
    
I regret none of this automation. As long as fundamental design choices remain constant (or at least change slowly), vertical changes to the code — changes to the JSON that result in the creation/deletion of code, scales smoothly.    
    
Life gets much more interesting as we scale horizontally — changing the templates that sit between the JSON and the final C# files. Diabolical bugs can lurk by manner of a single typo in a template, waiting to reproduce themselves across hundreds of files with the click of a button. Whitespace changes annoy to the extreme. We can (usually do) anticipate and surmount many of these hurdles. Frequent commits guarantee that noise doesn’t swallow avoidable faults. When refactoring a template, always make sure that optimization changes to the template code always produce identical outputs before other refactorings.    
    
Changes to behavior lead to the nightmarish class of faults that lurk under the bed and are far more insidious: changes to validation logic, unique validation, how objects are cached inside a database transaction, when/how permissions are evaluated. It is trivial to change a template in such a way that crippling performance issues are immediately injected into the app. Fail to properly dispose an object, and the app can begin gradually leaking memory. Hunting down these bugs is time consuming and tedious.    
    
Orthogonally (yet again), the technology choices we made to drive code generation are somewhat dated by today’s standards. T4 would be far easier to maintain than our antiquated format; and we have inconsistencies in where template code is defined: inside the template itself or in an external “code generation” project which has to be compiled before the templates can run. While it all technically works, the cost to maintain the code gen library has only increased over time — and the cost to refactor the project to use modern frameworks is difficult to justify relative to providing new features that someone would be willing to pay for.    
    
We have extracted a tremendous amount of value from this design decision overall, so I cannot condemn the philosophical approach we have taken; but within that bundle of ideas, design and technology choices there are multitudes of tiny decisions that certainly could have been better planned with an eye toward the future. The elegance of the design choices that I admire sadly must coexist with a certain amount of spaghetti, but such is the nature of development at times.    
    
I suspect these dichotomies are somewhat ubiquitous in nature, but I do look forward to the opportunity to either perfect our approach to code generation or discover a superior path forward.    
