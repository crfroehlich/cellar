---
author: CF
categories:
  - metadata
  - retrospective
  - code
  - generation
comments: true
description: "Sometimes best intentions and good ole fashioned elbow grease are no match against the tyrannical fist of Reality..."
draft: false
image: images/2019-07-10-code-generation-fail-an-all-the-kings-horses-tragedy.jpg
layout: post
subtitle: ' An all the king’s horses tragedy'
title: 'Code Generation Fail: An all the king’s horses tragedy'
toc: false
---
    
Sometimes best intentions and good ole fashioned elbow grease are no match against the tyrannical fist of Reality. In my [previous post](https://blog.luddites.me/2019/Code-Generation--Connecting-T4-to-Entity-Framework-Core), I discussed some of the initial ambitions and achievements in my attempt to convert the Entity Framework Core + MVC project into a dynamically generated template using T4. The good news? T4 is a perfectly adequate way to generate code from templates. The bad news? There is no way to make this completely integrated in a pure .NET Core project. What does this mean, and why is this a problem?    
    
### The Story    
    
Full disclosure: if you want something approximating a tldr; just skip to the next section.    
    
For starters, the beauty of .NET Core is that I can compile and run a .NET Core app as easily on my Pixelbook as I can on my Windows devices. Getting a Core app up and running is just a `git clone` and `dotnet build` away! This simplicity is bruised, battered and beaten when we can’t run the transforms required to generate that code. In an ideal world, when I run `dotnet build` on the [AutoEcMvc](https://github.com/crfroehlich/autoEcMvc) solution, the CodeGeneration project would compile and output the generated code into the AutoEcMvc project, which would then compile.    
    
If this worked, the project could then drop seamlessly into Azure Pipelines which would automatically compile, test and deploy the project to my Azure app container! (It should be noted, I can still make this work, but it’s extra work that should be unnecessary).    
    
Before I dive into the full autopsy of the deceased dream, I’ll explain my temporary solution and a few alternatives that I considered. For reasons that I’ll explain momentarily, the only way for even `msbuild` to work with a T4 project is to base the project on .NET Framework. In order to keep the T4 free from compilation issues (because Text Transformation happens _before_ build, you can find yourself in a situation where the solution cannot compile without some minor hacking), I find it useful to keep the C# backing code for the templates in its own project. Unfortunately, the minimum project type that will work is .NET Standard (.NET Framework will also work, but the goal is to get as close to .NET Core as possible). For these reasons, my current solution’s solution has three projects, in build order: CodeGeneration (.NET Standard) > T4 (.NET Framework) > AutoEcMvc (.NET Core). CodeGeneration has only the utility functions required for the T4 project, which has only the metadata JSON and TT files, and AutoEcMvc has the user defined and generated content together. In another chapter, we’ll remove generated code from source control entirely, but for now I’m leaving it in place as a way to easily review and compare changes with the original project.    
    
With this configuration, msbuild works seamlessly and I’ll update my Azure Pipeline to use msbuild and get my CI/CD plans back on track. I’m less than thrilled with having to throw `dotnet build` under the bus, but I’ve also spent more hours trying to crack this nut than I want — and I have whales need fryin’.    
    
Before I completely abandoned the dream of a pure .NET Core solution, I explored a few options. There is [Scripty](https://github.com/daveaglick/Scripty), which “lets you use Roslyn-powered C# scripts for code generation. You can think of it as a scripted alternative to T4 templates.” While it sounds promising, the project hasn’t been updated in a while and seems to have outstanding issues with .NET Core. There is also the issue of documentation — any [examples](https://github.com/daveaglick/Scripty/issues/104) of converting a T4 template to Scripty would be enormously helpful. Another promising option was [T5](https://github.com/atifaziz/t5), but no amount of tinkering prevailed against the ineffable and inscrutable refusal from Core to comply. Finally, there is [scaffolding](https://docs.microsoft.com/en-us/aspnet/core/tutorials/razor-pages/page?view=aspnetcore-2.2&tabs=visual-studio) built into ASP.NET Core, but this is specific to Razor pages and not really a way to keep generated code in sync with metadata in the way I want to use it.    
    
Absent other ideas, T4 still seems like the best approach without a major rewrite. I should note, my specific use of T4 is not terribly complicated, and it would be easy to ditch the templates altogether and simply write the code entirely in C#, but then it would cease to be generic and defeat the purpose of this little exercise. I may emerge from this experiment with a different opinion, but for now I’ll assume that T4 will remain the backbone of the project (until proven differently).    
    
As always, this has been a long winded series of asides, a slow and drifting detour from the main point: T4 and .NET Core do not mix. The latter does not abide the former. There is hostility, vitriol and threats of malice aforethought between the two. How do I know this?    
    
### My Current “Solution”    
    
To understand the problems with trying to migrate T4 into .NET Core, I think it useful to explain the challenges presented in .NET Framework. There are two paths to text transformation.    
    
The first is a developer convenience baked directly into Visual Studio since at least 2015. When you add a new `.tt` file into a project, VS immediately recognizes the template and offers a few convenience options for you as a developer: (1) whenever you save changes to the template, VS immediately runs text transform and the output is regenerated, (2) VS gives context menu options to manually transform (aka Run Custom Tool) and to debug the template, which will launch the debugger and allow you to set breakpoints anywhere, and (3) VS offers the option to run all transforms at the project/solution level. All of these features are hugely beneficial to the development of templates. But. And this is a big “but”.    
    
The second is through `msbuild` which is what I would normally use in my CI pipeline. Msbuild launches text transform in a different context than VS, which means that the relative paths (e.g. `$(SolutionDir)` )that work in VS no longer work from msbuild.    
    
In order to reconcile the two different behaviors, we need to make some changes to the `.csproj` file. Unfortunately, a project using .NET Framework has to be unloaded to be edited, then reloaded. Personally, I edit the project file directly in VS Code and let VS trigger the reload warning after I save changes. This is tedious, but it eliminates a few right-clicks.    
    
If we start with a [T4 import](https://github.com/crfroehlich/AutoEcMvc/blob/master/T4/templates/imports.ttinclude) that looks like this:    
    
```t4    
#><#@ assembly name="$(SolutionDir)CodeGeneration\\bin\\netstandard2.0\\CodeGeneration.Dll"    
#><#@ assembly name="$(SolutionDir)CodeGeneration\\bin\\netstandard2.0\\Newtonsoft.Json.Dll"    
#><#@ import namespace="Newtonsoft.Json"    
#><#@ import namespace="Newtonsoft.Json.Linq"    
#><#@ import namespace="CodeGeneration" #>    
```    
    
we have two immediate problems. `$(SolutionDir)` cannot resolve outside of VS, and our 3rd party dependency on Newtonsoft will not be available from the `bin` directory. Starting with the latter, you can add this into a property group in the project file:    
    
```xml    
<PropertyGroup>    
  <CopyLocalLockFileAssemblies>true</CopyLocalLockFileAssemblies>    
</PropertyGroup>    
```    
    
Now, all reference binaries will be copied into the build output directory.    
    
Next, the challenge of resolving the path to that output directory can be solved as well. A custom mapping is required, which is also possible with a little XML markup:    
    
```xml    
<PropertyGroup>    
  <targetFolder>$(MSBuildProjectDirectory)\\..\\CodeGeneration\\bin\\netstandard2.0</targetFolder>    
</PropertyGroup>    
<ItemGroup>    
  <T4ParameterValues Include="targetFolder">    
    <Value>$(targetFolder)</Value>    
  </T4ParameterValues>    
</ItemGroup>    
```    
    
Now, I can update the T4 imports to:    
    
```xml    
#><#@ assembly name="$(targetFolder)\\CodeGeneration.Dll"    
#><#@ assembly name="$(targetFolder)\\Newtonsoft.Json.Dll"    
```    
    
And this will work seamlessly between VS and msbuild. There are a few other changes to make, but these are much more clearly documented elsewhere. For reference here, you also need:    
    
```xml    
<Import Project="$(MSBuildToolsPath)\\Microsoft.CSharp.targets" />    
<PropertyGroup>    
  <!-- Get the Visual Studio version: -->    
  <VisualStudioVersion Condition="'$(VisualStudioVersion)' == ''">16.0</VisualStudioVersion>    
  <!-- Keep the next element all on one line: -->    
  <VSToolsPath Condition="'$(VSToolsPath)' == ''">$(MSBuildExtensionsPath32)\\Microsoft\\VisualStudio\\v$(VisualStudioVersion)</VSToolsPath>    
</PropertyGroup>    
<!-- This is the important line: -->    
<Import Project="$(VSToolsPath)\\TextTemplating\\Microsoft.TextTemplating.targets" />    
<PropertyGroup>    
  <TransformOnBuild>true</TransformOnBuild>    
  <OverwriteReadOnlyOutputFiles>true</OverwriteReadOnlyOutputFiles>    
  <TransformOutOfDateOnly>false</TransformOutOfDateOnly>    
  <RunPostBuildEvent>Always</RunPostBuildEvent>    
</PropertyGroup>    
```    
    
This is the final glue to ensure that you get identical behavior when compiling the project from any direction.    
    
Once all these pieces are in place, msbuild should **just work**.    
    
References:    
    
- [T4 Parameter Directive](https://docs.microsoft.com/en-us/visualstudio/modeling/t4-parameter-directive?view=vs-2019)    
- [Pass build context data into the templates](https://docs.microsoft.com/en-us/visualstudio/modeling/code-generation-in-a-build-process?view=vs-2019#parameters)    
- [TransformOnBuild](https://github.com/clariuslabs/TransformOnBuild) (I didn’t end up using this solution, but exploring the project helped me resolve some issues)    
    
### My Struggle    
    
The next time I run into this class of problem, I definitely want to track all of my uncommitted changes and correlate each attempted fix with the relevant errors that occur which drive me to all the URLs and searches for answers as I attempt each new iteration of the fix. Then, ideally, I could blog about each stage of the journey with a little more coherence. For now and for the sake of the reader, I will simply list all of the relevant bits (that I can find in my browser history) that have led me to the conclusion that (as of this moment) T4 and Core cannot coexist.    
    
Incomplete Solutions:    
    
- [Re-implementation of T4 in Mono](https://github.com/mono/t4)    
- [T4 Templates at Build Time with Dotnet Core](https://notquitepure.info/2018/12/12/T4-Templates-at-Build-Time-With-Dotnet-Core/)    
- [T4Executer](https://marketplace.visualstudio.com/items?itemName=TimMaes.ttexecuter)    
    
Relevant GitHub Issues:    
    
- [dotnet build fails to generate TypeGeneration.cs on linux](https://github.com/xamarin/TorchSharp/issues/27)    
- [Add MSBuild targets package](https://github.com/mono/t4/issues/12)    
- [Several FileNotFoundException using Newtonsoft.Json inside T4 template](https://github.com/dotnet/core/issues/2743)    
- [FileNotFoundException (System.Runtime, Version=4.2.1.0) when reflecting in T4 template](https://github.com/dotnet/core/issues/2000)    
- [Package type ‘DotnetCliTool’ is not supported by project](https://github.com/nil4/dotnet-transform-xdt/issues/16)    
- [PlatformNotSupportedException with dotnet-t4-project-tool](https://github.com/mono/t4/issues/42)    
- [build failing — and what worked for me](https://github.com/dotnet-websharper/core/issues/903)    
    
Relevant StackOverflow Issues:    
    
- [T4 subtemplates TransformText() not working](https://stackoverflow.com/questions/17170080/t4-subtemplates-transformtext-not-working)    
- [TextTemplating target in a .Net Core project](https://stackoverflow.com/questions/47691299/texttemplating-target-in-a-net-core-project)    
- [The imported project “C:\\Program Files\\dotnet\\sdk\\2.1.201\\Microsoft\\VisualStudio\\v15.0\\WebApplications\\Microsoft.WebApplication.targets” was not found](https://stackoverflow.com/questions/50471751/the-imported-project-c-program-files-dotnet-sdk-2-1-201-microsoft-visualstudio)    
    
Microsoft Docs:    
    
- [TextTransformation Class](https://docs.microsoft.com/en-us/dotnet/api/microsoft.visualstudio.texttemplating.texttransformation?view=visualstudiosdk-2019)    
- [FileNotFoundException (System.Runtime, Version=4.2.1.0) when reflecting in T4 template in .NET Core 2.1 app](https://developercommunity.visualstudio.com/idea/535990/filenotfoundexception-systemruntime-version4210-wh.html)    
    
As always, I hope this has helped someone with a similar quest. Please feel free to correct anything I have missed, suggest corrections or alternatives, or otherwise reach out to collaborate on solutions for this journey.    
