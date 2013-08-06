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
