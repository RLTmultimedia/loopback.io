---
title: "Creating models from unstructured data"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Creating-models-from-unstructured-data.html
summary:
---

For unstructured data such as that in NoSQL databases and REST services, you can create models using _instance introspection_.  Instance introspection creates a model from a single model instance using [`buildModelFromInstance()`](http://apidocs.strongloop.com/loopback-datasource-juggler/#datasource-prototype-buildmodelfrominstance).  

The following data sources support instance introspection: 

*   [MongoDB data sources ](/doc/{{page.lang}}/lb2/MongoDB-connector.html)
*   [REST data sources](/doc/{{page.lang}}/lb2/REST-connector.html)
*   [SOAP data sources](/doc/{{page.lang}}/lb2/SOAP-connector.html)

For example:

**/server/boot/script.js**

```
module.exports = function(app) {
  var db = app.dataSources.db;

  // Instance JSON document
  var user = {
    name: 'Joe',
    age: 30,
    birthday: new Date(),
    vip: true,
    address: {
      street: '1 Main St',
      city: 'San Jose',
      state: 'CA',
      zipcode: '95131',
      country: 'US'
    },
    friends: ['John', 'Mary'],
    emails: [
      {label: 'work', id: 'x@sample.com'},
      {label: 'home', id: 'x@home.com'}
    ],
    tags: []
  };

  // Create a model from the user instance
  var User = db.buildModelFromInstance('User', user, {idInjection: true});

  // Use the model for CRUD
  var obj = new User(user);

  console.log(obj.toObject());

  User.create(user, function (err, u1) {
    console.log('Created: ', u1.toObject());
    User.findById(u1.id, function (err, u2) {
      console.log('Found: ', u2.toObject());
    });
  });
});
```
