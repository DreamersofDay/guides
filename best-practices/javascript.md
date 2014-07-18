# JavaScript Best Practices

## General
* CSS classes that are dom manipulated should always be prefixed with `.js-`. These classes should never be styled.
* Embed data into the view using `data-` attributes. 

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

* Don't hardcode URLs endpoints in JS. See the above example. 
* Prefer API endpoints to serve JSON representation of objects. Be RESTful.
* Prefer the minimal updates. Don't do business logic on the frontend. 
```coffeescript
# An interaction is completed by setting used: true. We need to send an email and mark the used_at timestamp

# Good. Let the server handle sending email and marking used_at after setting the used flag. 
$.ajax
  url: url
  data: 
    interaction:
      used: true

# Bad. Putting the flow of business logic on the frontend. 
$.ajax(
  url: url
  data: 
    interaction:
      used: true
      used_at: new Date()
).then (interaction) ->
  # There are chances of the first update being successful but the email not being sent because of failure here. 
  $.ajax
    url: send_email_url
    data:
      interaction_id: interaction.id
```    

* Use `head: no_content` if a response is not needed, otherwise render a JSON representation of the object. 

## Write MVC Code
* Don't use `$.ready()`. We have a dispatcher that p
* Always namespace your code. For example, a JS utility object in Octopus, `Octopus.IntegerToWord.convert()`
* In absence of frameworks, we have a JS code dispatcher. Look for `dispatcher.js`
* Dispatcher integrates with Rails by using executing 

## Use CoffeeScript Only
* Don't use backticks. Repeat - Don't ever use backticks.

* Use `@get('field')` for `this.get()` and `$(@)`. Never use `$(this)` because in certain contexts, `@` is actually `_this`

* Use fat arrow(=>) to bind context. Do not use `bind()` for consistency. As JavaScript matures, the Coffee compiler might eventually compile `=>` to `bind()`.
