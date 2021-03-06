---
title: "Partitioning users with realms"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Partitioning-users-with-realms.html
summary:
---

By default, the LoopBack User model manages all users in a global namespace. It does not isolate different applications. In some cases, you may want to partition users into multiple namespaces so that different applications have separate users. LoopBack uses _realms_ to support: 

*   Users and applications belonging to a single global realm (or no realm). 
*   Distributing users and applications to multiple realms. A user or application can belong to only one realm. Each realm can have many users and many applications. 
*   Each application is a unique realm and each user belongs to an application (via a realm). 

Each application or user instance still has a unique ID across realms. When an application/user is signed up, it can be assigned to a realm. The [`User.login()`](http://apidocs.strongloop.com/loopback/#user-login) function:

*   Honors the realm property from the user credential.
*   Allows the realm to be extracted from the prefix of username/email.

Two settings in the User model control the realms:

*   `realmRequired` (Boolean): true | false (default)
*   `realmDelimiter` (string): If configured, the email or username can be prefixed as _<realm><realmDelimiter><username or email>_, for example, myRealm:john or myRealm:[john@sample.com](mailto:john@sample.com).  If not present, no prefix will be checked against username or email. 

For example,

**server/model-config.json**

```js
"User": {
  "dataSource": "db",
  "options": {
    "realmRequired": true,
    "realmDelimiter": ":"
  }
},
```

When realms are enabled, you must provide a `realm` property when you call `User.create()`, for example:

```js
User.create({
  realm: 'myRealm',
  username: 'john',
  email: 'john@sample.com',
  password: 'my-password'
}, callback);
```

To login a user within a realm, the credentials should include the realm property too.

```js
User.login({
  realm: 'myRealm',
  username: 'john',
  password: 'my-password'
}, callback);
```

If the realmDelimiter is configured (for example, to ":"), the login allows the realm to be passed in as prefix to the username or email.

```js
User.login({
  username: 'myRealm:john',
  password: 'my-password'
}, callback);
```
