---
title: "Define model relations"
lang: zh
layout: page
keywords: LoopBack
tags:
sidebar: zh_lb2_sidebar
permalink: /doc/zh/lb2/Define-model-relations.html
summary:
---

{% include important.html content="

**Prerequisites**:

*   Install StrongLoop software as described in [安装 StrongLoop](https://docs.strongloop.com/pages/viewpage.action?pageId=6095101)
*   Follow [LoopBack初级教程](https://docs.strongloop.com/pages/viewpage.action?pageId=6095006).

**Recommended**: Read [LoopBack 核心概念](https://docs.strongloop.com/pages/viewpage.action?pageId=6095111).

" %}

Relations among models enable you to query related models and perform corresponding validations.

Individual models are easy to understand and work with. But in reality, models are often connected or related.   For applications with multiple models, you typically need to define _relations_ between models.  

{% include note.html content="

If you followed the previous step in the tutorial, go to [Introducing model relations](/doc/zh/lb2/Define-model-relations.html).

If you're just jumping in, follow the steps below to catch up...

" %}

Get the app (in the state following the last article) from GitHub and install all its dependencies:

```
$ git clone https://github.com/strongloop/loopback-getting-started-intermediate.git
$ cd loopback-getting-started-intermediate
$ git checkout step2
$ npm install
```

## Introducing model relations

{% include note.html content="

LoopBack supports many different kinds of model relations, including: [BelongsTo](/doc/zh/lb2/BelongsTo-relations.html), [HasMany](/doc/zh/lb2/HasMany-relations.html), [HasManyThrough](/doc/zh/lb2/HasManyThrough-relations.html), and [HasAndBelongsToMany](/doc/zh/lb2/HasAndBelongsToMany-relations.html), among others. For more information, see [Creating model relations](/doc/zh/lb2/Creating-model-relations.html).

" %}

In the Coffee Shop Reviews app, the models are related as follows:

*   A coffee shop has many reviews.
*   A coffee shop has many reviewers.
*   A review belongs to a coffee shop.
*   A review belongs to a reviewer.
*   A reviewer has many reviews.

## Define relations

Now, you're going to define these relationships between the models.  In all there are five relations.  Once again, you'll use `slc loopback`, but this time you'll use the `relation` sub-command ([relation generator](/doc/{{page.lang}}/lb2/Relation-generator.html)).  For each relation, enter:

`$ slc loopback:relation`

The tool will prompt you to provide the information required to define the relation, as summarized below.

**A coffee shop has many reviews**; No through model and no foreign key.

```
? Select the model to create the relationship from: CoffeeShop
? Relation type: has many
? Choose a model to create a relationship with: Review
? Enter the property name for the relation: reviews
? Optionally enter a custom foreign key:
? Require a through model? No
```

**A coffee shop has many reviewers**; No through model and no foreign key.

```
? Select the model to create the relationship from: CoffeeShop
? Relation type: has many
? Choose a model to create a relationship with: Reviewer
? Enter the property name for the relation: reviewers
? Optionally enter a custom foreign key:
? Require a through model? No
```

**A review belongs to a coffee shop**; No foreign key.

```
? Select the model to create the relationship from: Review
? Relation type: belongs to
? Choose a model to create a relationship with: CoffeeShop
? Enter the property name for the relation: coffeeShop
? Optionally enter a custom foreign key:
```

**A review belongs to a reviewer**; foreign key is `publisherId`.

```
? Select the model to create the relationship from: Review
? Relation type: belongs to
? Choose a model to create a relationship with: Reviewer
? Enter the property name for the relation: reviewer
? Optionally enter a custom foreign key: publisherId
```

**A reviewer has many reviews**; foreign key is `publisherId`.

```
? Select the model to create the relationship from: Reviewer
? Relation type: has many
? Choose a model to create a relationship with: Review
? Enter the property name for the relation: reviews
? Optionally enter a custom foreign key: publisherId
? Require a through model? No
```

## Review the model JSON files

Now, look at `common/models/review.json`.  You should see this:

**common/models/review.json**

```js
...
"relations": {
  "coffeeShop": {
    "type": "belongsTo",
    "model": "CoffeeShop",
    "foreignKey": ""
  },
  "reviewer": {
    "type": "belongsTo",
    "model": "Reviewer",
    "foreignKey": "publisherId"
  }
},
...
```

Likewise, `common/models/reviewer.json` should have this:

**common/models/reviewer.json**

```js
...
"relations": {
  "reviews": {
    "type": "hasMany",
    "model": "Review",
    "foreignKey": "publisherId"
  }
},
...
```

And `common/models/coffee-shop.json` should have this:

**common/models/coffee-shop.json**

```js
...
"relations": {
  "reviews": {
    "type": "hasMany",
    "model": "Review",
    "foreignKey": ""
  },
  "reviewers": {
    "type": "hasMany",
    "model": "Reviewer",
    "foreignKey": ""
  }
},
...
```

Next: Continue to [Define access controls](/doc/{{page.lang}}/lb2/Define-access-controls.html).
