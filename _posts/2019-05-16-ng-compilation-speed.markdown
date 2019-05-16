---
title: "🏇 A simple tip to improve angulars compilation speed"
layout: post
date: 2019-05-16 12:00
image: /assets/images/ng-compilation.png
headerImage: true
projects: false
description: "We increase node's maximum memory to speed up the angular build process"
tag:
    - npm
    - angular
    - node.js
category: blog
author: marcelcremer
externalLink: false
---

Do you have a large angular application that takes too long to compile / serve / compile tests? This little thing helped me:

### Increase the node process memory

By default, a node process can take up to 1,76GB of RAM. This is by original design of Javascript, where the assumption was, that a single thread (which node is) will not exceed a total of 2 GB RAM.

However, things got a bit more complex the last few years, because JavaScript frameworks evolved and allowed to compose different libraries to a framework or in our case - the angular compile mechanism.

And if your compilation process reaches this limit, the node garbage collector will start alternating with the compilation process, freeing up just enough space so that the next piece of work can be executed by ngc.

If you're like me, you might think: Why the hell should ngc ever exceed this limit?!?

The short answer is: Because it does many things for you 😃

Okay, a bit more specific:

-   it bundles assets into your final build
-   it keeps several hundred files in memory to do tree-shaking
-   it compiles scss-files
-   it organizes chunks into module / vendor chunks
-   it seperates often used code pieces for shared use into a common chunk
-   ...and much much more :)

So how do we get rid of that free memory / do a bit / free memory-cycle?

### Increasing the node memory limit with max_old_space_size

Node has a simple flag to change the maximum RAM consumption before the garbage collector will start to agressively free up memory. It's like

node --max*old_space_size=\_size in MB*

As I have 24GB of RAM in my machine, I don't have any problem to assign 8 GB to the node process. But I also don't want to lose the comfort of the ng cli, so how to automatically assign the parameter to ng commands?

The solution is also simple, but maybe not obvious:

We call the ng cli directly out of the node*modules folder using a node script. I called this variant "nghm" *(for ng high memory)\_ and built it like this:
`{ ... "nghm": "node --max_old_space_size=8096 ./node_modules/@angular/cli/bin/ng" ... }`

Having this script in mind, we can transform "ng serve" into

`npm run nghm -- serve`

which will now consume up to 8GB RAM. A production build could look like

`- npm run nghm -- build --prod --progress=false --aot=true --vendor-chunk`

_And the Numbers?_
Well, that might depend from project to project, but in our project this particular change has reduced the compilation time from ~3:26min to ~1:36min.

It might be influenced by how large your assets are, how many files are compiled and so on, but maybe you'd like to give it a try.
