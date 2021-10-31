---
author: CF
categories:
  - metadata
  - retrospective
  - code
  - generation
comments: true
description: >-
  I have enjoyed working with
  [T4]httpsdocsmicrosoftcomenusvisualstudiomodelingcodegenerationandt4text...
draft: false
image: images/2019-07-08-code-generation-connecting-t-4-to-entity-framework-core.png
layout: post
subtitle: ' Connecting T4 to Entity Framework Core'
title: 'Code Generation: Connecting T4 to Entity Framework Core'
toc: true
---
    
I have enjoyed working with [T4](https://docs.microsoft.com/en-us/visualstudio/modeling/code-generation-and-t4-text-templates?view=vs-2019) in my .NET projects, and I have wanted to start exploring Entity Framework Core with ASP.NET Core. Fortunately, Microsoft has an excellent [suite of tutorials](https://docs.asp.net/en/latest/data/ef-mvc/intro.html) which makes it dead simple to get a sample project up and running. I jumped straight to the end and began by copying down the project code from the [ASP.NET Core Documentation](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final) repo and I started my own git repo my new and intuitively named [AutoEcMvc](https://github.com/crfroehlich/AutoEcMvc) (Automatic EF Core over MVC) project. I even went the extra mile and set up a free Azure account to host the project at [https://autoecmvc.luddites.me](https://autoecmvc.luddites.me/). The challenges of connecting the GitHub project to a CI/CD pipeline for hosting on Azure will be addressed in a follow up blog post.    
    
This project is still in the early prototype phases, but the goal is to maintain feature parity with the original Microsoft tutorial project while eliminating the majority of the code. That said, it has most of the essential building blocks for dynamically generating models and controllers using code generation powered by T4.    
    
Before diving in further, the most obvious question to answer is: What problems does this project solve? If you have ever needed to design, extend or maintain an application that either sits on relational database or exposes a concrete set of APIs, you may be familiar with the sudden weakening of the stomach as you realize just how many lines of boiler plate code need to be written in order to get your new classes or modifications to classes ready for consumption by the compiler and the consumers of the app. Factories must be updated, new view/model/controller classes must be written. Potentially hundreds of lines of code that must be manually maintained — hundreds of lines of code that are nearly identical in style and substance to the classes that came before. With code generation, we can eliminate all of that boiler plate and focus exclusively on just the (usually) small subset of things that are actually different from our new or extended schema changes.    
    
T4 is one code generation/templating solution that can help us solve this problem. Since we’ll be using .NET Core as our project type, we never have to worry about adding/removing new files to/from the project — that happens automatically. T4 has some idiosyncrasies and shortcomings (also the subject of a follow-up blog post), but it works well enough for this prototype that I am comfortable recommending it as a starting point.    
    
A few goals and guiding principles that I have applied:    
    
- Code Generation should live in a separate project from the main application projects. This becomes essential as if your generated code does not compile, you can quickly get stuck in a chicken/egg/rooster situation where you cannot -pile to fix the compile.    
- All code templates should use structure data format for defining how to generate code. For this project, I use JSON files which represent each unique entity to be translated into actual project code.    
- Use partial classes everywhere possible to allow extension and custom properties logic.    
- Use abstract base classes to allow injecting custom logic inside the generated code. This is critical if you need custom logic to execute inside a constructor, for example, and do not want to overly complicate the templates.    
- Keep as much business logic out of the template as possible.    
- Separate generated code from user code and annotate generated code appropriately.    
    
If you look through the structure of [AutoEcMvc](https://github.com/crfroehlich/AutoEcMvc), you’ll notice that there are two main projects: AutoEcMvc, which is the app that gets deployed and CodeGeneration, which is just the templating logic. I picked .NET Standard as the project type for CodeGeneration, as I ran into numerous issues trying to get the project working using .NET Core. I will revisit that in the near future.    
    
Perhaps the single most import part of code gen is the structured data format in use. I need to be able to map my JSON back to a C# class that can be used deterministically in T4, so my [Course](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Transforms/schema/Course.json) class is defined in JSON as:    
    
```json    
"Description": "A course represents a college course that a student can take",    
"DisplayName": "Title",    
"HasControllers": true,    
"ControllerMethods": [ "Index", "Details", "Create", "Edit", "Delete" ],    
"Name": "Course",    
"DatabaseGeneratedOption": "None",    
"PrimaryKeyDisplayName": "Number",    
"Columns": [...]    
```    
    
Each entity has a collection of columns, and a column looks like:    
    
```json    
"Description": "Unique name of the course",    
"Length": 50,    
"MinimumLength": 3,    
"Name": "Title",    
"Order": 0,    
"Type": "string"    
```    
    
Honestly, it took quite a few iterations before I landed on the current data structure — and there is still quite a bit of cruft that can be removed to polish this. I started with the model classes, because they are the simplest and require the least amount of logic overall. I opted to use a numbered naming convention for the templates as a clue to the developer on the order in which things should be done: note this is simply an opinion and has no effect on the text transform step of compile — these templates can (and should always be able to) be transformed in any order. So [02_models.tt](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Transforms/templates/02_Models.tt) is the first step of the process.    
    
First, include our imports for T4:    
    
`<#@ include file="imports.ttinclude" #><#`    
    
You’ll notice that I frequently start an opening tag immediately after a closing tag `#><#` . This is [just a trick](https://stackoverflow.com/a/19860881) to avoid injecting extra white space into the output file.    
    
Second, load our schema from JSON:    
    
```csharp    
var tables = BuildMethods.GetJsonFilesAsTables(Path.GetDirectoryName(Host.TemplateFile) + "\\\\..\\\\schema");    
foreach(var table in tables) {    
```    
    
It becomes easier as you scale the templates up and out to use utility methods. I have opted to store these in a static [BuildMethods](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Core/BuildMethods.cs) class for two reasons: (1) you get compile time errors that make sense if the methods don’t compile and (2) debugging is much simpler inside a C# class. You could just as easily write these in a shared T4 include, which I also do for the [SaveOutput](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Transforms/templates/saveoutput.ttinclude) methods. Either way works just as well. The plus sides of defining methods in T4 in this way are (1) you don’t need the “BuildMethods” prefix and (2) the methods do not need to compile before the transform executes. Personally, I generally prefer the static BuildMethods approach for most of my logic.    
    
Third, we run through vanilla T4 concepts to construct our output model class. There are far better resources on the ins and outs of T4 to waste your time here, and I’d rather focus on the nuances of trying to model entities from JSON. Perhaps the biggest hurdle at the model layer is getting the property types correct. If you look at the final output for [Course.cs](https://github.com/crfroehlich/AutoEcMvc/blob/master/AutoEcMvc/Generated/Models/Course.cs#L23), you’ll notice that it doesn’t have many properties, but most of them are special in one or more ways.    
    
First, we have `CourseID` which is the primary key for the Course table. Almost every table should have some unique identifier, and we can probably assume that it will be named `{tableNmae}Id` for the majority of cases ([Person](https://github.com/crfroehlich/AutoEcMvc/blob/master/AutoEcMvc/Generated/Models/Person.cs#L19) is an exception which required some [custom logic](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Transforms/templates/02_Models.tt#L7)).    
    
Second, we have a relationship to `Department` which has the database column `DepartmentID` as well as the materialized, convenience property of `Department`. For almost all primitive properties (int, string, DateTime, etc), I use a column definition that references the primitive explicitly, e.g. `"Type": "int?"`. This works well for the most part — there are a variety of cases where we need extra information, such as to specify a string [min/max length](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Transforms/schema/Course.json#L14). Relationships, whether one-to-one, one-to-many or many-to-many require a bit more work. I opted to use a dedicated set of types for these: `["relationship", "relagtionship?", "relationships"]` which I can then use inside my [type building logic](https://github.com/crfroehlich/AutoEcMvc/blob/master/CodeGeneration/Core/BuildMethods.cs#L328) to write out the type exactly the way I want without having to specify multiple synonymous properties in JSON — that is, we could have accomplished this by defining two separate properties in the column definition, but that seemed wasteful and a potential pitfall for anyone maintaining the code — forget either property and the app won’t work as expected.    
    
Third, we write out a model file for each JSON file that’s been processed:    
    
```t4    
<#    SaveOutput(table.Name + ".cs", "..//..//..//AutoEcMvc//Generated//Models");  } //foreach(var table in tables) #><#@ include file="saveoutput.ttinclude" #>    
```    
    
Since I want to have a single model template generate many model outputs, this handles the writing of the individual files. T4 still generates a single, backing file — which is annoying. I set the template output extension to `.ignore`, which generates a `02_models.ignore` file after each compilation. A post build event then deletes all .ignore files. It’s not the most elegant solution to the problem, but it works well enough.    
    
I attacked the problem of generating the model classes from T4 by solving each class individually. From the starting project, I attacked each class in alphabetical order and committed the changes when I had each new class compiling and there were no regressions in the UI. If I were to start over, I would have written the tests first; but since the goal of this refactoring was to produce nearly 100% identical code to the original using code gen — most of the work was spent looking at the diffs to spot the bugs in my transform logic.    
    
I would also note that this approach would be very different if I cared about producing the most well designed code/architecture. There are numerous edge cases I shimmed into the logic to handle inconsistent naming conventions; for example, if I were to write these classes from scratch, I would guarantee all properties were named consistently and could then throw away some of the custom logic required in transforms. Since the goal was to mimic the original code as closely as possible, I feel relatively at peace with the choices made to get here.    
    
Next time, I’ll talk about some of the challenges with templating the controller classes — work that I have nearly finished. Following that, views are next up and potentially the most challenging.    
    
If you enjoy working with T4 or other code generation technologies, let me know what your experience has been like. If you have any tips or tricks (especially trying to get T4 transforms running under `dotnet build`), please, please do let me know.    
