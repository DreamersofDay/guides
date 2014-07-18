# JavaScript Best Practices

## General
* Always use `.js-<class>` to indicate a class for JS manipulation.
* Don't hardcode URLs in JS code. Put them on data attributes and grab them in JS.
```haml
  / In articles#show
  .js-article-view{ data: { url: article_path(@article) } }
```

```coffeescript
  # Get the update path
  url = $('.js-article-view').data("url")
  $.ajax
    method: "PATCH"
    data: { ... }
    url: url
```

## Write MVC Code
* Do not write code directly in `javascript $.ready()`
* In absence of frameworks, we have a JS code dispatcher. Look for `dispatcher.js`


## Use CoffeeScript Only
* Don't not use backticks. Repeat - Don't use backticks.

* Use `@get('field')` for `this.get()` and `$(@)`. Never use `$(this)` because in certain contexts, `@` is actually `_this`

* Use fat arrow(=>) to bind context. Do not use `bind()` for consistency. As JavaScript matures, the Coffee compiler might eventually compile `=>` to `bind()`.
