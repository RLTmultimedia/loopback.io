---
title: "Define access controls"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Define-access-controls.html
summary:
---

{% include important.html content="

**Prerequisites**:

*   Install StrongLoop software as described in [安装 StrongLoop](https://docs.strongloop.com/pages/viewpage.action?pageId=6095101)
*   Follow [LoopBack初级教程](https://docs.strongloop.com/pages/viewpage.action?pageId=6095006).

**Recommended**: Read [LoopBack 核心概念](https://docs.strongloop.com/pages/viewpage.action?pageId=6095111).

" %}

Access controls determine which users are allowed to read and write model data or execute methods on the models

{% include note.html content="

If you followed the previous step in the tutorial, go to [Introducing access controls](/doc/zh/lb2/Define-access-controls.html).

If you're just jumping in, follow the steps below to catch up...

" %}

Get the app (in the state following the last article) from GitHub and install all its dependencies:

```
$ git clone https://github.com/strongloop/loopback-getting-started-intermediate.git
$ cd loopback-getting-started-intermediate
$ git checkout step3
$ npm install
```

## Introducing access controls

LoopBack apps access data through models, so controlling access to data means putting restrictions on models; that is, specifying who or what can read and write the data or execute methods on the models.   LoopBack access controls are determined by _access control lists_ or ACLs. For more information, see [Controlling data access](/doc/{{page.lang}}/lb2/Controlling-data-access.html).

You're going to set up access control for the Review model.  

The access controls should enforce the following rules:

*   Anyone can read reviews, but you must be logged in to create, edit, or delete them.
*   Anyone can register as a user; then log in and log out.
*   Logged-in users can create new reviews, and edit or delete their own reviews; however they cannot modify the coffee shop for a review.

## Define access controls

Once again, you'll use `slc loopback`, but this time you'll use the `acl` sub-command; for each ACL, enter:

`$ slc loopback:acl`

The tool will prompt you to provide the required information, as summarized below.

**Deny everyone all endpoints**.  This is often the starting point when defining ACLs, because then you can selectively allow access for specific actions.

```
? Select the model to apply the ACL entry to: Review
? Select the ACL scope: All methods and properties
? Select the access type: All (match all types)
? Select the role: All users
? Select the permission to apply: Explicitly deny access
```

**Now allow everyone to read reviews**.

```
? Select the model to apply the ACL entry to: Review
? Select the ACL scope: All methods and properties
? Select the access type: Read
? Select the role: All users
? Select the permission to apply: Explicitly grant access
```

**Allow authenticated users to write to the create a review**; that is, if you're logged in, you can add a review.

```
? Select the model to apply the ACL entry to: Review
? Select the ACL scope: A single method
? Enter the method name: create
? Select the role: Any authenticated user
? Select the permission to apply: Explicitly grant access
```

Now, **enable the author of a review (its "owner") to make any changes to it**.

```
$ slc loopback:acl
? Select the model to apply the ACL entry to: Review
? Select the ACL scope: All methods and properties
? Select the access type: Write
? Select the role: The user owning the object
? Select the permission to apply: Explicitly grant access
```

## Review the review.json file

When you're done, the ACL section in `common/models/review.json` should look like this:

```js
... 
"acls": [{
  "accessType": "*",
  "principalType": "ROLE",
  "principalId": "$everyone",
  "permission": "DENY"
}, {
  "accessType": "READ",
  "principalType": "ROLE",
  "principalId": "$everyone",
  "permission": "ALLOW"
}, {
  "accessType": "EXECUTE",
  "principalType": "ROLE",
  "principalId": "$authenticated",
  "permission": "ALLOW",
  "property": "create"
}, {
  "accessType": "WRITE",
  "principalType": "ROLE",
  "principalId": "$owner",
  "permission": "ALLOW"
}],
...
```

Next: Continue to [Define a remote hook](/doc/{{page.lang}}/lb2/Define-a-remote-hook.html).
