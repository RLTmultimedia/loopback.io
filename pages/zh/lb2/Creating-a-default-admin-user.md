---
title: "Creating a default admin user"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Creating-a-default-admin-user.html
summary:
---

LoopBack does not define a default administrator user, however you can define one when the application starts, as illustrated in the [loopback-example-access-control](https://github.com/strongloop/loopback-example-access-control) example.  Specifically, the example includes code in [`server/boot/create-model-instances.js`](https://github.com/strongloop/loopback-example-access-control/blob/master/server/boot/create-model-instances.js) that:

*   Creates several users, along with instances of other models
*   Defines relations among the models.
*   Defines an admin role,
*   Adds a role mapping to assign one of the users to the admin role.

Because this script is in `server/boot`, it is executed when the application starts up, so the admin user will always exist once the app initializes.

The following code creates three users named "John," "Jane," and "Bob, then (skipping the code that creates projects, project owners, and project team members) defines an "admin" role, and makes Bob an admin:

**/server/boot/script.js**

```
User.create([
    {username: 'John', email: 'john@doe.com', password: 'opensesame'},
    {username: 'Jane', email: 'jane@doe.com', password: 'opensesame'},
    {username: 'Bob', email: 'bob@projects.com', password: 'opensesame'}
], function(err, users) {
    if (err) return debug('%j', err);
    ...
    // Create projects, assign project owners and project team members
    ...
    // Create the admin role
    Role.create({
      name: 'admin'
    }, function(err, role) {
      if (err) return debug(err);
      debug(role);

      // Make Bob an admin
      role.principals.create({
        principalType: RoleMapping.USER,
        principalId: users[2].id
      }, function(err, principal) {
        if (err) return debug(err);
        debug(principal);
      });
    });
  });
};
```

The project model JSON (created by running `slc loopback:acl`, the [ACL generator](/doc/{{page.lang}}/lb2/ACL-generator.html)) file specifies that the admin role has unrestricted access to view projects (`GET /api/projects`):

**/common/models/model.json**

```js
... {
  "accessType": "READ",
  "principalType": "ROLE",
  "principalId": "admin",
  "permission": "ALLOW",
  "property": "find"
},
...
```
