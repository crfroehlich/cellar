---
author: CF
categories:
  - work
  - sweat
  - lodge
comments: true
description: It was the spring of our discontent...
draft: false
image: images/2013-04-07-the-quick-molting-sweat-lodge-of-the-soul.png
layout: post
title: 'The Quick, Molting Sweat Lodge of the Soul'
toc: false
---
    
It was the spring of our discontent.    
    
One of our customers, call them Massive Dynamics, needed a dedicated resource to provide application/server/database support/administration as well as an interface into development. I had joined the team as that very chimera to fill the need in early 2008.    
    
Eight months in, and I knew enough about the application, the data, the business requirements and the infrastructure to do something that resembled contribution. I began to get sporadic and vague reports that "performance is slow" and "application is down" and "doesn't work". Naturally, I couldn't reproduce any of these async requests by the time I received the callback; nonetheless, I water-boarded the log files and generally came up empty. Enhanced interrogation doesn't work in application support either, it seems.    
    
The complaints quickly grew by enough pay grades, that it became clear "something", "needed", "to be done." It was early January, if I recall; when we hatched the perfect plan. Perfect, because we still didn't understand the problem, because we had no control of MD's internal infrastructure or latency between end users to web servers to databases, because we had no mechanism to try to internally reproduce MD's production load on the scale of MD's data. Perfect for all of these reasons.    
    
So our perfect plan was simply this: if the only thing we can control is the software we deploy to the web servers, let's make the web server response indestructible. Let's build a proxy service to intermediate the request/response cycle: guarantee the user's GETs always return a response; guarantee the user's POSTs all, eventually commit to the database. By spring of 2009, we were ready to deploy the proxy to MD production.    
    
At least Napoleon got to see Moscow.    
    
[+David](http://plus.google.com/114734695547160645038) and [+Phil](http://plus.google.com/106826698447901956266) and Cliff and I began sweat lodging to the oldies multiple times per week.  The application was Schrödinger's epileptic cat: simultaneous alive and dead and seizing regardless. Proxy's only saving grace is that it persisted user's sessions, and if we responded fast enough to each failure, we could restart the failed service and the end user would be spared. I scripted a rolling blackout across the services to preempt failure, and [+David](http://plus.google.com/114734695547160645038) and I alternated responding to untrapped, early AM failures.    
    
The yin yanged, and we finally pushed back: from an engineering perspective, it's inconceivable that this suspension bridge should fail to accommodate 200 cars/minute. MD finally conceded, "Did we mention we have monsoon season and the cars are [Yugos](http://en.wikipedia.org/wiki/Zastava_Koral#Criticism_and_response)?" As it turns out, it doesn't actually matter what you do to stabilize an application that shares a database instance and some tablespace with 400 other applications, if and when 1 or more of those other applications decides to go rogue.    
    
So the soul molts.    
    
Only hindsight lends real transparency into this comedy of errors. If any lessons are to be learned, I'll defer to your good judgment to elaborate upon them. What interests me more is the process by which we can become dislodged, for better or worse. Over the months that spanned this drama, I thought of little else. Even as we bought our first house and moved. Even as Eli took to speech. The crisis was all consuming. All the walls were painted panic.    
    
And suddenly, as if by magic, you tilt your head one tiny fraction of a degree to the north, and the whole of the world shifts, dropping the very part of you that succommed to panic with it. The road leads ever onward.    
    
By itself, this tale is little more than a passable bar story between engineers; but the cycle of it never abates. I've spent the last eight months at nearly 7 on the 10 scale. "10," I tell my doctors, "would roughly feel like having your legs sawn off with a steak knife." Between the pain and me has floated a raft of heavy narcotics and medications. The tumor was finally extracted from my spinal column last month, and I can get out of bed without first snorting a line of percocet.    
    
As the puppeteers of my dopamine reactors begin to resign, it starts to become clear that yet another phase of life has become dominated by a single arity: pain. While Antasi has begun to speak, and all manner of other important things and decisions and events have fallen in and out of scope, they've all been obfuscated behind the constant threat of something apprehending agony.    
    
Then it ends. You tilt your head.    
