---
title: "REST connector"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/REST-connector.html
summary:
---

**See also**:

See also:

*   [REST connector API doc](http://apidocs.strongloop.com/loopback-connector-rest/)
*   Example [loopback-faq-rest-connector](https://github.com/strongloop/loopback-faq-rest-connector)

## Overview

The LoopBack REST connector enables applications to interact with other REST APIs using a template-driven approach. It supports two different styles of API invocations:

*   [Resource CRUD](/doc/{{page.lang}}/lb2/REST-connector.html)
*   [Defining a custom method using a template](/doc/{{page.lang}}/lb2/REST-connector.html)

## Installation

In your application root directory, enter:

`$ npm install loopback-connector-rest --save`

This will install the module from npm and add it as a dependency to the application's [package.json](http://docs.strongloop.com/display/LB/package.json) file.

## Creating a REST data source

Use the [Data source generator](/doc/{{page.lang}}/lb2/Data-source-generator.html) to add a REST data source to your application.  

`$ slc loopback:datasource`

When prompted, scroll down in the list of connectors and choose **REST services (supported by StrongLoop)**.   This adds an entry to [datasources.json](/doc/{{page.lang}}/lb2/datasources.json.html) (for example):

```js
...
"myRESTdatasource": {
  "name": "myRESTdatasource",
  "connector": "rest"
}
...
```

## Configuring a REST data source

Configure the REST connector by editing `datasources.json` manually (for example using the Google Maps API):

**/server/datasources.json**

```js
...
"geoRest": {
  "connector": "rest",
  "debug": "false",
  "operations": [{
    "template": {
      "method": "GET",
      "url": "http://maps.googleapis.com/maps/api/geocode/{format=json}",
      "headers": {
        "accepts": "application/json",
        "content-type": "application/json"
      },
      "query": {
        "address": "{street},{city},{zipcode}",
        "sensor": "{sensor=false}"
      },
      "responsePath": "$.results[0].geometry.location"
    },
    "functions": {
      "geocode": ["street", "city", "zipcode"]
    }
  }]
}
...
```

For a REST data source, you can define an array of operation objects to specify the REST API mapping. Each operation object can have the following properties:

*   template: See the [description](/doc/{{page.lang}}/lb2/REST-connector.html).
*   functions: An object that maps a Node.js function to a list of parameter names. For example, a Node.js function geocode(street, city, zipcode) will be created so that the first argument will be value of street variable in the template, second for city and third for zipcode.

## Configure options for request

The REST connector uses [https://github.com/request/request](https://github.com/request/request) module as the HTTP client. You can configure or pass options to [https://github.com/request/request#requestoptions-callback](https://github.com/request/request#requestoptions-callback).

The options can be configured by the _**options**_ property at two levels:

*   Data source level (common to all operations)
*   Operation level (specific to the declaring operation)

For example, the following example set Accept and Content-Type to application/json for all requests. It also sets strictSSL to false so that the connector allows self-signed SSL certificates.

**/server/datasources.json**

```js
{
  connector: 'rest',
  debug: false,
  options: {
    "headers": {
      "accept": "application/json",
      "content-type": "application/json"
    },
    strictSSL: false,
  },
  operations: [{
    template: {
      "method": "GET",
      "url": "http://maps.googleapis.com/maps/api/geocode/{format=json}",
      "query": {
        "address": "{street},{city},{zipcode}",
        "sensor": "{sensor=false}"
      },
      "options": {
        "strictSSL": true,
        "useQuerystring": true
      },
      "responsePath": "$.results[0].geometry.location"
    },
    functions: {
      "geocode": ["street", "city", "zipcode"]
    }
  }]
}
```

## Resource CRUD

If the REST API supports create, read, update, and delete (CRUD) operations for resources, such as users or orders, you can simply bind the model to a REST endpoint that follows REST conventions.

For example, the following methods would be mixed into your model class:

*   create: POST /users
*   findById: GET /users/:id
*   delete: DELETE /users/:id
*   update: PUT /users/:id
*   find: GET /users?limit=5&username=ray&order=email

For example:

**/server/script.js**

```js
var ds = loopback.createDataSource({
  connector: require("loopback-connector-rest"),
  debug: false,
  baseURL: 'http://localhost:3000'
});

var User = ds.createModel('user', {
  name: String,
  bio: String,
  approved: Boolean,
  joinedAt: Date,
  age: Number
});

User.create(new User({
  name: 'Mary'
}), function(err, user) {
  console.log(user);
});

User.find(function(err, user) {
  console.log(user);
});

User.findById(1, function(err, user) {
  console.log(err, user);
});

User.update(new User({
  id: 1,
  name: 'Raymond'
}), function(err, user) {
  console.log(err, user);
});
```

## Defining a custom method using a template

Imagine that you use a web browser or REST client to test drive a REST API; you will specify the following HTTP request properties:

*   method: HTTP method
*   url: The URL of the request
*   headers: HTTP headers
*   query: Query strings
*   responsePath: an optional JSONPath applied to the HTTP body. See [https://github.com/s3u/JSONPath](https://github.com/s3u/JSONPath) for syntax of JSON paths.

Then you define the API invocation as a JSON template. For example:

```js
template: {
      "method": "GET",
      "url": "http://maps.googleapis.com/maps/api/geocode/{format=json}",
      "headers": {
        "accepts": "application/json",
        "content-type": "application/json"
      },
      "query": {
        "address": "{street},{city},{zipcode}",
        "sensor": "{sensor=false}"
      },
      "responsePath": "$.results[0].geometry.location"
    }
```

The template variable syntax is:

`{name=defaultValue:type}`

The variable is required if the name has a prefix of ! or ^

For example:

<table>
  <tbody>
    <tr>
      <th>Variable definition</th>
      <th>Description</th>
    </tr>
    <tr>
      <td><pre><code>'{x=100:number}'</code></pre></td>
      <td>Define a variable x of number type and default value 100</td>
    </tr>
    <tr>
      <td><pre><code>'{x:number}'</code></pre></td>
      <td>Define a variable x of number type</td>
    </tr>
    <tr>
      <td><pre><code>'{x}'</code></pre></td>
      <td>Define a variable x</td>
    </tr>
    <tr>
      <td><pre><code>'{x=100}ABC{y}123'</code></pre></td>
      <td>
        <p>Define two variables x and y. The default value of x is 100\. The resolved value will be a concatenation of x, 'ABC', y, and '123'. For example, x=50, y=YYY will produce '50ABCYYY123'</p>
      </td>
    </tr>
    <tr>
      <td><pre><code>'{!x}'</code></pre></td>
      <td>Define a required variable x</td>
    </tr>
    <tr>
      <td><pre><code>'{x=100}ABC{^y}123'</code></pre></td>
      <td>Define <span>two variables x and y. The default value of x is 100\. y is required.</span></td>
    </tr>
  </tbody>
</table>

To use custom methods, you can configure the REST connector with the `operations` property, which is an array of objects that contain `template` and `functions`. The `template` property defines the API structure while the `functions` property defines JavaScript methods that takes the list of parameter names.

```js
var loopback = require("loopback");

var ds = loopback.createDataSource({
  connector: require("loopback-connector-rest"),
  debug: false,
  operations: [{
    template: {
      "method": "GET",
      "url": "http://maps.googleapis.com/maps/api/geocode/{format=json}",
      "headers": {
        "accepts": "application/json",
        "content-type": "application/json"
      },
      "query": {
        "address": "{street},{city},{zipcode}",
        "sensor": "{sensor=false}"
      },
      "responsePath": "$.results[0].geometry.location"
    },
    functions: {
      "geocode": ["street", "city", "zipcode"]
    }
  }]
});
```

Now you can invoke the geocode API as follows:

`Model.geocode('107 S B St', 'San Mateo', '94401', processResponse);`

By default, LoopBack REST connector also provides an 'invoke' method to call the REST API with an object of parameters, for example:

` Model.invoke({street: '107 S B St', city: 'San Mateo', zipcode: '94401'}, processResponse);`
