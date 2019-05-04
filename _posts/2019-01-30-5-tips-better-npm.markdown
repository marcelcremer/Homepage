---
title: "5 Tips for better NPM script organization"
layout: post
date: 2019-01-30 12:00
image: assets/images/5tips.png
headerImage: true
projects: false
blog: true
#hidden: true # don't count this post in blog pagination
description: "5 Tips to organize your growing npm scripts"
category: npm, javascript, node.js
author: marcelcremer
externalLink: false
---

Not too long ago, it was pretty normal to use grunt, gulp and other tools to organize building, testing and other tasks in JS-Projects. However, this required people to install, maintain and understand different build systems, structures and configurations.

### NPM Scripts to the rescue!

Everyone who uses npm as their package manager already has a package.json and the ability, to run scripts from there. So what is more obvious, than using npm scripts for automated tasks around the project?

However, since the npm scripts is in no way opinionated about modeling complex build systems, the organisation and consistency of the scripts is in responsibility of the maintaining developer. And here are some tips on what you could do.

### 1. Consistent script names

If you start using NPM for several tasks, your script section might grow pretty fast. You might have for example scripts for unit testing, for integration testing, the same with coverage reports and also as watch-mode for development. If it grows organic, you might end up with something like

```json
{
    "build": "...",
    "test": "...",
    "test-watch": "...",
    "integration-test": "...",
    "watch-integration-test": "...",
    "test-coverage": "...",
    "test-integration-coverage": "...",
    "build-prod": "..."
}
```

**_Urks..._**

You might not notice it in the first place, but more often you'll misspell whatever you want to do, e.g. you write

```bash
npm run integration-test-watch
```

instead of

```bash
npm run watch-integration-test
```

triggering the well known "Command not found"-Error.

So somewhen you get the idea to change it the other way around, and guess what? The next time you try it, you write it **wrong again** , because you don't have a system...yet :)

So what we need is some naming scheme, that is consistent in your own aesthetical point of view. For example, I use something like:

```json
{
    "build": "...",
    "build:production": "...",
    "test": "...",
    "test:coverage": "...",
    "test:watch": "...",
    "test:integration": "...",
    "test:integration:coverage": "...",
    "test:integration:watch": "..."
}
```

So I start off with what I want to do, followed by specifying it or calling additional behaviours. As the order always stays the same, I'm not so likely to misspell things.

Also I have some "internal" scripts, that are only used for DRY purposes inside of the package.json. Usually I let them start with a hashtag, so that people don't get the idea to use them directly. For example:

```json
{
    "#:copy:assets": "...",
    "#:copy:configuration-templates": "...",
    "#:generate:polyfills": "..."
}
```

### 2. Performance

One thing people often don't care about is performance. If we think of unit tests, it's pretty obvious that every second counts. So if you press ctrl+s about 10 times a minute, and every testing cycle takes 3 seconds, you spend **about half your time (10 \* 3 = 30 seconds) waiting for your test results**! Scary, isn't it?

If you're in a typescript environment, the code also needs to be compiled into javascript before the tests etc. can be executed. So take a minute upfront to think about your task execution, before wasting hours and hours with waiting.

-   Use modules like [concurrently](https://www.npmjs.com/package/concurrently) whenever you're able to execute tasks in parallel (e.g. having your typescript-compiler watch .ts files, and your tests watch the output .js-files).
-   Try to avoid that different tasks do the same (e.g. integration testing and unit testing both trigger the typescript compiler)
-   Make granular scripts for different tasks and chain them in convenience scripts
-   review your scripts from time to time to see, what bottlenecks you encounter

### 3. Platform independence

Because I primarily develop on windows (blame me if you want...), I really hate it when I want to contribute to some Open Source project, and the

```bash
npm run start
```

script fails because it's relying on some unix command. Try to use node-implementations whenever you can, to avoid platform specific code.

For Example:

-   Use [rimraf](https://www.npmjs.com/package/rimraf) instead of rm -rf
-   Use [copyfiles](https://www.npmjs.com/package/copyfiles) instead of copying via OS commands
-   ... you get the idea ;)

Even worse is the usage of npm packages that rely on **native OS calls**, which need to be compiled with node-gyp before being usable.

If you don't believe me, have a look at [stackoverflow](https://stackoverflow.com/search?q=node-gyp) on how many problems node-gyp creates, **before** you decide to use some native OS library to "asynchronously add 2 numbers" (or other curiousities like that)!

I know, some scenarios need native libraries and that's perfectly fine, but please think twice before adding them as a dependency. If the cause is for example performance, provide a _"slow, platform-indepedent, nodejs"-way_ as default and add the _"native call"-way_ as a **peer dependency**, so people can decide themselves, if the performance is worth the native module compilation.

Always prefer options over "might not be working for someone", because the persons you otherwise push away might have sent you an incredible pull request.

### 4. Additional parameters

Sometimes I see projects, that have the exact same script multiple times implemented, just to add different parameters. That is fine if you're providing convenience methods (e.g. serve to start a development webserver), but if you have a dozen of them, you might also think about people just parametrizing your scripts.

So instead of

```json
{
    "start": "node server.js",
    "start:integration-port": "node server.js --port=4202",
    "start:https": "node server.js --https=true",
    "start:integration-port:https": "node server.js --port=4202 --https"
}
```

you could also provide a single start command, and use \-\- to pass additional parameters to the script. That can be used like

```bash
npm run start
npm run start -- --port=4202
npm run start -- --https=true
npm run start -- --port=4202 --https=true
```

### 5. NPX instead of single command scripts

Since NPM 5, NPM supports a tool called "NPX". What it basically does is, it executes a script from your dependencies as node executable.

For example instead of writing

```bash
node ./node_modules/typescript/bin/tsc
```

you could write

```bash
npx tsc
```

and would execute your local typescript compiler (more information [here](https://www.npmjs.com/package/npx)).

Sometimes I look into projects, that have 20 NPM scripts or something, and some of them are

```json
{
    // ...
    "webpack": "webpack",
    "tsc": "tsc"
    // ...
}
```

which are pretty useless. Use NPX and tighten your package.json even more.

Do you have some more tips? How do you structure your package.json?
