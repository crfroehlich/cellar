---
author: CF
badges: true
categories:
  - logging
  - code
  - tech
comments: true
description: "For quite a while now, I have wanted to update my blog..."
draft: false
image: images/2020-10-04-blogging-to-the-oldies.jpg
layout: post
title: Blogging to the Oldies
toc: false
---
    
## Reality, Meet Opportunity    
    
For quite a while now, I have wanted to update my blog, do some more writing (both personally and professionally) and generally flex some unused muscles a bit. Fortuna often has sadistic tendencies as some can attest. Consider the recent toppling of the [NS8 empire](https://www.sec.gov/litigation/litreleases/2020/lr24905.htm) [1](https://www.wsj.com/articles/former-head-of-cyber-fraud-detection-startup-ns8-arrested-on-fraud-charges-11600469207), [2](https://www.pacermonitor.com/public/case/36301258/United_States_Securities_and_Exchange_Commission_v_Rogas_et_al), [3](https://www.forbes.com/sites/davidjeans/2020/09/18/how-a-cyber-fraud-company-ceo-raised-123-million-in-months---and-got-arrested-for-fraud/amp), [4](https://www.reviewjournal.com/business/sec-charges-former-ceo-of-tech-company-with-fraud-2123503/), [5](https://www.justice.gov/usao-sdny/press-release/file/1317641/download), [6](https://www.linkedin.com/pulse/ns8-demonstrates-how-employment-data-can-valuable-richard-stiennon) and a [great fall of nepotism](https://www.formds.com/issuers/ns8-inc) with [recent developments](https://www.justice.gov/usao/justice-101/preliminary-hearing) and [former employee actions](https://www.reviewjournal.com/business/laid-off-workers-sue-tech-company-citing-lack-of-advance-notice-2124501/). So many links to criminal conspiracy to defraud and so little daylight; regardless, I have had more than a little time to navel gaze in recent weeks.    
    
I've long been frustrated by the available tools for blogging. Some of these goals compete with each other and some are onerous due to costs--I want as much of my platform to be free (both as in beer and as in speech) as possible _and_ I want as much control over my platform as possible _and_ I want to have features like commenting and search and analytics _and_ there can be no ads and the word _Jekyll_ may not be invoked. I have an old [Blogger site](https://hiking.luddites.me), which I desperately want to retire; and I have a [Medium site](https://medium.com/@christopher.r.froehlich) which I am increasingly frustrated with: from the inability to post articles in the past to Mediums idiotic approach to monetization--what was initially cool about medium is no longer interesting to me.    
    
So I set out to figure out how to create the blog platform you, dear reader, are currently (hopefully) enjoying.    
    
### Goals    
    
Broadly speaking, I want to be able to meet these objectives:    
    
- Easy to add new posts    
- Easy to update existing posts    
- Keep revision history of edits    
- Blog is searchable    
- Commenting in some form is possible, and comments are stored in my control    
- No ads (at least for now)    
- Service is free for me to host my content and free for consumers    
- I can more or less change anything about the blog at whim    
- Fast to manage    
    
### Non-starters    
    
I considered a number of options, and I won't go too deep into the pros and cons so much as leave breadcrumbs for others to follow (in no particular order):    
    
1. [Medium](https://medium.com): it looks nice, but I have to surrender too much control. It's not free as in speech or free as in lattes.    
1. [Blogger](https://blogger.com): it looks dated. It does most of what I need, but it's slow to manage and cumbersome to maintain.    
1. [Ghost](https://ghost.org): it used to be a nice competitor to Medium, but the target audience for users no longer includes me. It's free to self-host, but non-trivial to manage.    
1. All other self-hosted solutions (Ghost included): while I _can_ run anything on my local systems, I don't want to depend on my personal equipment to run my site and I don't want to have to deal with security (although services like [Ngrok](https://ngrok.com) and [Pagekite](https://pagekite.net/) are awesome and definitely worth your time).    
1. Cloud hosting options are a mixed bag when looking for free. You can get lots of usage out of [Azure](https://azure.com) on the initial free options, but eventually you have to pay.    
1. WordPress/Drupal/etc. Will never use; will not investigate.    
    
## Solution    
    
One "blog" concept that I've always liked is by [@raganwald](https://twitter.com/raganwald) is his use of [Github](https://github.com/raganwald/raganwald.github.com/tree/master/_posts) for organizing content. I have long considered this approach as it checks off a lot of boxes:    
    
- [x] Complete control    
- [x] Free as in latkes and free as in peaceful protests    
- [x] It's git, so it's all under control    
    
The perceived negatives I have had in the past seem to be less of an issue these days with the emergence of a few factors (some relatively new, some a bit older):    
    
- [Github Pages](https://pages.github.com/): a free static content website based on the content of a branch of your repository. Commonly the `gh-pages` branch, which is how this blog is served.    
- [Github Actions](https://github.com/features/actions): Manage your updates to Github Pages automatically    
- [Github Codespaces](https://github.com/features/codespaces): Everything you need to manage your site, in your browser. This is a very nice to have--you can do this anywhere/anytime already with other tools.    
- [GatsbyJs](https://www.gatsbyjs.com/): A pretty slick static site generator based on React. Lots of extensions and pluggability.    
- [Comments](https://github.com/utterance/utterances): Using a tool called Utterances, which is just a convenient wrapper around Github Issues--commenting is free and easy and completely under my control.    
- [Search](https://www.algolia.com/): This is the only 3rd party piece that I cannot take and move just anywhere, but it's free as in pancakes for my purposes, so I'll content with it for now and/or until I need something better.    
    
### Getting There    
    
I did some basic soul googling for `blog github pages -jekyll` and quickly found [Creating my blog with Gatsby and Github Pages](https://codesandtags.github.io/blog/creating-my-blog-with-gatsby-and-github-pages), which was more than enough to get me going. I explored a variety of [Gatsby Templates](https://www.gatsbyjs.com/starters/?v=2) (once I had decided that Gatsby would meet all my needs) and I picked the [Gitbook Starter](https://www.gatsbyjs.com/starters/hasura/gatsby-gitbook-starter/) template as a base. There were a variety of things I needed to change--most notably the logic for generating the sidebar tree, which required comparing the [template repo](https://github.com/hasura/gatsby-gitbook-starter) with the [demo repo](https://github.com/hasura/learn-graphql/tree/master/tutorials/graphql/intro-graphql/tutorial-site).    
    
Most of my time was occupied fixing annoyances--I spent more than a few hours wrestling with the [gh-pages](https://github.com/tschaub/gh-pages) module that I use for automating deploys to, you guessed it, Github Pages. The production build process for Gatsby is much slower than I would like, which is especially annoying in the early phases of development when you're making lots of minor tweaks and changes. Combine that with trying to guarantee that I cache bust my site with new deploys, the general speed of build+deploy, local caching issues, remote caching issues--and I easily spent most of a day refreshing my browser in a state of uncertainty as to whether I was testing the right version of my code. Once I generally worked through my self-inflicted CI/CD pipeline issues, I am very satisfied with what I was able to throw together over a day and a half.    
    
## Outstanding Concerns    
    
One of the fun bits of doing all this work is that it's a beautiful combination of development work, writing and designing--all of which I love. I get to keep polishing my dev chops as I work through my backlog of blog posts to migrate as well as explore new technologies and solutions to my own little set of concerns. I've historically struggled to find a development problem that aligns with my personal interests, and for the first time I feel like I have the beginnings of a solution here.    
    
Still, much remains todo if I want to consider this feature complete:    
    
- Add analytics so I can measure traffic.    
- Some form of management for comments.    
- Find alternate ways to authenticate for comments so not everyone needs to have a Github account to comment.    
- Fix some rendering issues on page load.    
- Tagging and other basic "bloggy" features.    
- Migrate all my old content over.    
- Refactor the whole thing to TypeScript, because {reasons}.    
- Refactor the whole thing from TypeScript to Rust, because...why not?    
    
If any of this has been interesting, please let me know in the comments or in [the code, which is the blog, which is the code, which...](https://github.com/crfroehlich/blog)!    
