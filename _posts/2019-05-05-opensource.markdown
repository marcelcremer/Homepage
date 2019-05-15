---
title: "Why your Open Source Project might lack (my?) contribution (1)"
layout: post
date: 2019-05-05 12:00
image: /assets/images/opensource.png
headerImage: true
projects: false
description: "In this article I describe some things to consider when maintaining an Open source project"
tag:
    - opensource
    - organisation
    - management
category: blog
author: marcelcremer
externalLink: false
---

Many developers like to "give something back" to the community by supporting Open Source projects in one way or another. The types of contribution can vary widely, e.g.:

-   Reporting Issues and / or Feature Requests
-   Fixing things, they need themselves
-   Add some value (a new Feature, better documentation, ...)
-   "Swarm intelligence" by contributing to brainstormings or discussions
-   Testing (and giving feedback)

However, sometimes contributors are also repelled by various reasons. The following reasons are some of mine...

### How important it is, to have a low hurdle in your project

My first and top reason not to contribute to a project is...

**...not finding a good starting point!**

Like really, if you check out a project, maybe install dependencies and then the build doesn't work first try, there's a chance of about 90% percent for me to drop the idea of contribution.

Sometimes it takes dozens of steps to get the build running. Sometimes you find an issue where someone reports, that step xy is missing in the documentation and it's still not updated. Sometimes you need to cherry-pick ideas from different places to get something working.

**Why should I spend my time and diligence in contributing to something, that seems not important to the maintainer?**

Make it easy for contributors to take the first steps, document it well and keep it updated all the time. It's really important to support people, who want to support your project!

A good example for a low first run hurdle (node-based in this case):

-   Check out the project
-   npm i
-   copy conf.json* to conf.json and change database setttings *(json exists and just needs to be modified - easy!)\_
-   npm run serve
-   Success!

Now it's possible to play around with code while reading it, get instant feedback on what changes do and so on.

Keep also the following things in mind:

-   If you have unit tests (and I hope you have some), make them available as simple as the first compilation _(e.g. npm run test:watch in this case)_
-   if you have a database dependency, put the schema and initial seed in your VCS. No one wants to open an administration, execute ten random SQL files and maybe get a consistent database _(or not)_! Provide an automated script or create the database automatically on startup, if it doesn't exist!

### Keep your project fast!

> "Okay, so I got this library and it has a bug. I found the line of code in github already, so let's fix this real quick and send out a pull request:
> git clone xyz
> install dependencies...
> ...
> ...
> Okay, that took a while. Let's start the program to see, if I can reproduce the error.
> ...
> ...
> ...
> Ehm, will this ever finish?
> ...

If you want contributors to send Pull / Merge requests, **care for your project's performance**.

Sometimes the issue is obvious: Even from reading the code on the source code repository, you can put your finger on the line where the error happens.

However, to get a pull request, the contributor needs to

-   check out the code
-   install deps
-   maybe add a database / server / whatever
-   get it running
-   reproduce the error _(or write a test)_
-   fix the error
-   push it
-   create an issue
-   create a PR referencing the issue

If you ever see it from this perspective, you surely recognize that there's a lot of stuff to do _(to fix your "little" mistake)_.

If it takes about an hour to setup the development environment, have the first compilation done and get tests running, I tend to just drop the idea to create a fix and instead just open an issue. Even if I had the patience to create this fix, I wouldn't have the time for it in my daily business.

**So please keep the development cycle fast!**

Some ideas to achieve this:

-   Create some small projects that are working for themselves instead of providing one large monolith
-   Keep the dependencies small
-   Don't create 1 million fake entries in your initial database seed. If someone want's to do stress test, he could easily apply an additional seed
-   Run tests on the continous integration server - especially if they aren't easy to set up locally. It'll be much easier to throw something against the server and let him validate if everything is fine, than setting up some hard-to-setup-test-suites
-   if you have easy to use unit tests, make sure that they have a short feedback loop. If the suite takes 3 seconds to test and someone saves every 3 seconds, 50% of the time will be wasted for compilation / waiting for tests
-   if it's not possible to keep the whole test suite that fast, make it easy to isolate tests. In node / jasmine for example there's an fdescribe and fit that will only execute that particular test suite / test case. Make sure, that the isolated tests are as fast as possible

### Maintain your project

Everyone who puts up a project on github, gitlab or something is the so-called "maintainer", but many people don't really maintain their project.

To make this point really clear: No one expects an open source maintainer to be available 24/7 and always have an answer to any given topic.

However, if someone takes his time to carefully open an issue on your project, handle the issue itself carefully as well:

If you've seen the issue but don't have the time right now, write a short note that you've seen it and..._drum roll_...don't have the time to work on it right now! People like to be noticed by others, especially if they put some work in their issue. The answer

> "Hey, I saw your issue but I'm on a business trip atm. Coming back 2 u in a few days"

Is already some kind of appreciation to the creator, shows that you take him seriously and also that you _(and the project your maintaining)_ is still active.

The same is of course true for pull / merge requests. If you have 20 open requests without any notice or discussion, the 21st requests won't be opened,
because everyone will think: This project is unmaintained.

### End of Part 1

Because this article seems to grow rapidly and I still have some things noted here, I'm going to make a cut at this point.

I'd love to hear, if you also struggle with some of those things. I'd also like to hear if you want to read more :)

Discuss with me on [Twitter](https://twitter.com/mcremer_en) and follow me for more interesting blog articles.
