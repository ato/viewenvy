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
with [standard Groovy syntax](http://groovy.codehaus.org/User+Guide). For the most part you can treat 
Groovy just like Java with some optional sugar like being able to write `page.article.title` in place 
of `page.getArticle().getTitle()`.

Helpers
-------

* TODO: describe how to write a helper
* TODO: example of using a random Java utility library as a helper

### Partials and Layouts

* TODO: describe render helper
* TODO: describe yield, provide and contentFor helpers

### Future rendering

Sometimes there's a slow section of your page that's holding up rendering. Perhaps it needs to perform
some complex calculation or talk to a third party web service. You could use an AJAX request to run
the slow query asynchronously but ViewEnvy provides an easy alternative:

```jsp
<% future { %>
    You have ${ db.slowQuery() } tokens remaining.
<% } %>
```

The future helper writes a placeholder div with the class you specified and immediately continues
processing the rest of the page:

```html
<div id="future17"></div>
```

It schedules the content block for processing on a background thread. Once ViewEnvy finishes the
rest of the page it will flush to the browser and wait for any running futures. As each future
finishes ViewEasy will tack some JavaScript on the end of the page that replaces the placheholder div
with the appropriate content:

```html
<script>
  documemt.getElementById("async17").outerHTML = "You have 17 tokens remaining.";
</script>
```

You can also specify a CSS class name for styling the placeholder div:

```jsp
<style>
  .loading       { background-image: url('throbber.gif'); height: 25px; width: 25px; }
  .loading:after { content: "Loading..." }
</style>

<% future("loading") { %> ... <% } %>
```

Usage
-----

### Configuration

You'll always need to construct a ViewEnvy object:

```java
ViewEnvy views = new ViewEnvy();
```

You might like to give ViewEnvy a prefix and suffix to use when searching for templates on the classpath. Doing
so means you can use `views.render("home")` rather than `views.render("myapp/views/home.gsp")`:

```
views.setPrefix("myapp/views/");
views.setSuffix(".gsp");
```

By default the helpers in ViewEnvy.DEFAULT_HELPERS are available, but you might to like specify your own 
set of helper classes to use:

```java
Set<Class> helpers = new HashSet<>();
helpers.addAll(ViewEnvy.DEFAULT_HELPERS);
helpers.add(MyHelper.class);
helpers.add(org.apache.commons.lang3.text.WordUtils.class);
views.setHelpers(helpers);
```

### Standalone

ViewEnvy can be used standalone without any web-framework. Here's an example of rendering a view 
to `System.out`:

```
Writer out = new OutputStreamWriter(System.out);

Map model = new HashMap();
model.put("title", "Pudd'nhead Wilson");
model.put("author", "Mark Twain");

views.render("home", model, out);
```

### JAX-RS

ViewEnvy includes a MessageBodyWriter provider for use with JAX-RS. To use it return register ModelAndViewWriter
with your JAX-RS implementation:

```java
class MyApplication extends Application {
    final Set<Object> singletons = new HashSet<>();
    
    public MyApplication() {
        ViewEnvy views = new ViewEnvy();
        views.setPrefix("myapp/views/");
        views.setSuffix(".gsp");
        singletons.add(new ModelAndViewWriter(views));
    }
    
    public getSingletons() {
        return singletons;
    }
}
```

Then in your controllers return a ViewEnvy ModelAndView object:

```java
@GET
@Pah("/")
public ModelAndView homepage() {
    Map model = new HashMap();
    model.put("title", "My blog!");
    return new ModelAndView("home", model);
}
```

### Spring MVC

TODO: example
