ViewEnvy
========

ViewEnvy provides Rails-style views for Java web applications using Groovy as an expression language. Highlights:

* Simple static method helpers (with block syntax!) not tag libraries
* Partials with `provide` and `contentFor`
* Asynchronous template blocks

Syntax
------

ViewEnvy template syntax consists of `<% statement %>`, `<%= expression %>` and `${ expression }`. Nothing more. 
Here's an example:

```jsp
<h1>${article.title}</h1>
<ul>
<% for (section : article.toc) { %>
  <li><%= section.title %>
<% } %>
```

There is no special ViewEnvy expression language or tag/helper syntax. Those features are accompolished
with standard Groovy syntax. For the most part you can treat Groovy just like Javawith a couple of useful
shortcuts like writing `page.article.title` instead of `page.getArticle().getTitle()`.

Helpers
-------

* TODO: describe how to write a helper
* TODO: example of using a random Java utility library as a helper

Partials
--------

* TODO: describe render helper

Layouts
-------

* TODO: describe yield, provide and contentFor helpers

Asynchronous Content Blocks
--------------------------

* TODO: describe async helper

Configuration
-------------

* TODO: Standalone
* TODO: JAX-RS example
* TODO: Spring MVC example
