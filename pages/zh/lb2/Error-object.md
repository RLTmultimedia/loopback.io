---
title: "Error object"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Error-object.html
summary:
---

By convention, pass an `Error` object passed to callback functions as the err parameter.  In many cases, you can also pass a String.

For more information, see

*   [JavaScript Error object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) (Mozilla)
*   [Error Handling in Node.js](https://www.joyent.com/developers/node/design/errors) (Joyent)
*   [What is the error object?](https://docs.nodejitsu.com/articles/errors/what-is-the-error-object) (Nodejitsu)

The following table describes the properties of the error object.

<table>
  <tbody>
    <tr>
      <th>Property</th>
      <th>Type</th>
      <th>Description</th>
    </tr>
    <tr>
      <td>name</td>
      <td>String</td>
      <td>Name of the error.</td>
    </tr>
    <tr>
      <td>status</td>
      <td>String</td>
      <td>When the error occurs during an HTTP request, the HTTP status code.</td>
    </tr>
    <tr>
      <td>message</td>
      <td>String</td>
      <td>The error message.</td>
    </tr>
  </tbody>
</table>

{% include note.html content="

Any other properties of the error object are copied to the error output.

" %}
