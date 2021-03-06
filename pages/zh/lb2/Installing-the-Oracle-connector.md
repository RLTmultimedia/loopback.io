---
title: "Installing the Oracle connector"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Installing-the-Oracle-connector.html
summary:
---

## Overview

The Oracle connector depends on [strong-oracle](https://github.com/strongloop/strong-oracle) module as the Node.js driver for Oracle databases. Since strong-oracle is a [C++ addon](http://nodejs.org/api/addons.html), the installation usually requires the presence of C++ development tools to compile and build the module from source code. At runtime, strong-oracle also requires dynamic libraries from [Oracle Database Instant Client](http://www.oracle.com/technetwork/database/features/instant-client/index.html). To simplify the whole process, we use a helper module [LoopBack Oracle Installer](https://github.com/strongloop/loopback-oracle-installer) to take care of the binary dependencies.  The LoopBack Oracle installer downloads and extracts the prebuilt LoopBack Oracle binary dependencies into the parent module's `node_modules` directory and sets up the environment for the [Oracle Database Instant Client](http://www.oracle.com/technetwork/database/features/instant-client/index.html).

To install the Oracle connector, use the following command:

**shell**

`npm install loopback-connector-oracle --save`

{% include important.html content="

If you need to use the Oracle driver directly, see [https://github.com/strongloop/strong-oracle](https://github.com/strongloop/strong-oracle)

" %}

## Post installation setup

{% include warning.html content="

Before you run the application, you **MUST** configure the environment variable depending on the target platform to make sure the dynamic libraries from Oracle Instant Client will be available to your Node process.

" %}

### MacOS X or Linux

During npm install, the change is made in `$HOME/strong-oracle.rc`.

```
export DYLD_LIBRARY_PATH="$DYLD_LIBRARY_PATH:/Users/<user>/<myapp>/node_modules/loopback-connector-oracle/node_modules/instantclient" (MacOS X)

export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/Users/<user>/<myapp>/node_modules/loopback-connector-oracle/node_modules/instantclient" (Linux)
```

To activate the strong-oracle settings for your terminal window, add the following statements to $HOME/.bash_profile (or .profile depending on what shell you use):

**~/.bashrc | ~/.bash_profile**

```
if [ -f ~/strong-oracle.rc ]; then
  source ~/strong-oracle.rc
fi
```

You need to open a terminal window or run _source ~/.bash_profile_ to make the change take effect.

### Windows

The change is made to the PATH environment variable for the logged in user.  Please note the PATH setting will NOT be effective immediately. You have to activate it using one of the methods below:

1.  Log off the current user session and log in.
2.  Open Control Panel --> System --> Advanced System Settings --> Environment Variables. Examine the Path under User variables, and click OK to activate it. You need to open a new Command Prompt. Please run 'path' command to verify.

## Installation from behind a proxy server

{% include important.html content="

This feature is supported by loopback-oracle-installer vesion 1.1.3 or later.

" %}

If your system is behind a corporate HTTP/HTTPS proxy to access the internet, you'll need to set the proxy for npm before running 'npm install'. For example,

**shell**

```
$ npm config set proxy http://proxy.mycompany.com:8080
$ npm config set https-proxy http://https-proxy.mycompany.com:8080
```

If the proxy url requires username/password, you can use the following syntax:

**shell**

```
$ npm config set proxy http://youruser:yourpass@proxy.mycompany.com:8080
$ npm config set https-proxy http://youruser:yourpass@https-proxy.mycompany.com:8080
```

The proxy can also be set as part of the npm command as follows:

```
$ npm --proxy=http://proxy.mycompany.com:8080 install
$ npm --https-proxy=http://https-proxy.mycompany.com:8080 install
```

Please note that npm's default value for [proxy](https://www.npmjs.org/doc/misc/npm-config.html#proxy) is from the HTTP_PROXY or http_proxy environment variable. And the default value for [https-proxy](https://www.npmjs.org/doc/misc/npm-config.html#https-proxy) is from the HTTPS_PROXY, https_proxy, HTTP_PROXY, or http_proxy environment variable. So you can configure the proxy using environment variables too.

Linux or Mac:

**shell**

`HTTP_PROXY=http://proxy.mycompany.com:8080 npm install`

Windows:

**shell**

```
set HTTP_PROXY=http://proxy.mycompany.com:8080
npm install
```
