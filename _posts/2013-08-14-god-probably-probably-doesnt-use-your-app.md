---
author: CF
categories:
  - göd
  - code
comments: true
description: >-
  I propose we immediately deprecate the word "administrator" and its diminutive
  form "admin" from lan...
draft: false
image: images/2013-08-14-god-probably-probably-doesnt-use-your-app.jpg
layout: post
title: 'God Probably, *Probably*, Doesn''''t Use Your App'
toc: true
---
    
I propose we immediately deprecate the word "administrator", and its diminutive form "admin", from language. All language. And languages.    
    
Let me explain.    
    
I want to pick on [TeamCity](http://www.jetbrains.com/teamcity/) (TC) for a moment, as it represents the problem well. TC compiles, lints, tests and deploys your applications. Automatically. Continuously. It's wonderful; but it commits a sin so great that I feel it worth singling it out. Do not mistake me, TC is a miraculous product. I have a dozen unpenned posts to JetBrains in praise of their work; but.    
    
When you install TC and create the first user, that user is a global administrator. As a new user, I had to learn how to configure TC, to create the directives to build the code and the tasks to execute these directives. More than one person needed to access the app, so I needed to create more users. Only 30 minutes into testing, I had no idea how to configure new users and their permissions--so they became admins.    
    
You can imagine how this story evolves.    
    
I do not know how JetBrains fell into committing this sin, but I know how I do it. It starts with a problem. I start solving it by building a new application. Immediately, I realize more than one person needs to use the app at the same time. No problem: I create users. I discover the first rule boundary: not everyone should be allowed to delete--but all users can do everything! I need to distinguish between users. So I create "roles" (or groups or permission sets, etc.), and here I make the fatal mistake. I continue developing the app out from the perspective of the first user, the "admin" user.    
    
The "admin" is really just me, the developer of the application, the only person who is supposed to know everything about it. No other user should be expected to assume this role (though they will need to sudo it).    
    
One of the reasons my applications are harder to learn is for precisely this reason. I do not make clear, simple workflows to create roles and users; and I do not do this early and often. TC, like many apps, imposes a steep learning curve in exactly this way. I am not an "admin" of the app, just the manager of tasks; and all of the power user features that I get by virtue of being an admin are actually a hindrance--they make the app harder to learn, not easier.    
    
There is a place for godlike power in my application, but I now reserve it for myself--making the app. Real users should get roles that actually mean something--roles should imply a path through the app toward a solution.    
