---
title: "Using Browserify"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Using-Browserify.html
summary:
---

Use [Browserify](http://browserify.org/) to create a LoopBack API in the client.

The build step loads all configuration files, merges values from additional config files like `app.local.js` and produces a set of instructions that can be used to boot the application.

These instructions must be included in the browser bundle together with all configuration scripts from `models/` and `boot/`.

Don't worry, you don't have to understand these details. Just call `boot.compileToBrowserify()`, and it will take care of everything for you.

**Build file (Gruntfile.js, gulpfile.js)**

```js
var browserify = require('browserify');
var boot = require('loopback-boot');

var b = browserify({
  basedir: appDir,
});

// add the main application file
b.require('./browser-app.js', {
  expose: 'loopback-app'
});

// add boot instructions
boot.compileToBrowserify(appDir, b);

// create the bundle
var out = fs.createWriteStream('browser-bundle.js');
b.bundle().pipe(out);
// handle out.on('error') and out.on('close')
```
