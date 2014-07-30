Best Practices
==============

A guide for programming well.

General
-------

* Don't duplicate the functionality of a built-in library.
* Don't swallow exceptions or "fail silently."
* Don't write code that guesses at future functionality.
* Don't use abbreviations
* Don't generalise until you have at least 3 examples
* Don't use production credentials in a non-production environment
* Follow the conventions for a [12 factor app]
* [Exceptions should be exceptional].
* [Keep the code simple].

[12 factor app]: http://12factor.net/
[Exceptions should be exceptional]: http://www.readability.com/~/yichhgvu
[Keep the code simple]: http://www.readability.com/~/ko2aqda2

Object-Oriented Design
----------------------

* Don't use global variables.
* Avoid long parameter lists.
* Limit collaborators of an object (entities an object depends on).
* Limit an object's dependencies (entities that depend on an object).
* Prefer composition over inheritance.
* Prefer small methods. Between one and five lines is best.
* Prefer small classes with a single, well-defined responsibility. When a
  class exceeds 100 lines, it may be doing too many things.
* [Tell, don't ask].

[Tell, don't ask]: http://robots.thoughtbot.com/post/27572137956/tell-dont-ask

Ruby
----

* Use [Rubocop]. Exceptions will be specified in the rubocop.yml file.
* Don't use `options` as an argument, instead rely on named parameters.
* Prefer named parameters for 2 or more parameters.
* Avoid monkey-patching.

[Rubocop]: https://github.com/bbatsov/rubocop

Ruby Gems
---------

* Declare dependencies in the `<PROJECT_NAME>.gemspec` file.
* Reference the `gemspec` in the `Gemfile`.
* Use [Bundler] to manage the gem's dependencies.
* Use [Circle CI] for Continuous Integration, indicators showing whether GitHub
  pull requests can be merged, and to test against multiple Ruby versions.

[Bundler]: http://bundler.io
[Circle CI]: http://circleci.com

Rails
-----

* Avoid bypassing validations with methods like `save(validate: false)`,
  `update_attribute`, and `toggle`.
* Avoid using more than one model class in controllers
* Don't change a migration after it has been merged into master if the desired
  change can be solved with another migration.
* Don't reference an AR model class directly from a view.
* Don't return false from `ActiveModel` callbacks, but instead raise an
  exception.
* Don't use instance variables in partials. Pass local variables to partials
  from view templates.
* Don't use SQL or SQL fragments (`where('inviter_id IS NOT NULL')`) outside of
  models.
* Don't add routes that have no corresponding actions by using `only` to whitelist
  only the actions defined in controllers.
* Generate necessary [Spring binstubs] for the project, such as `rake` and
  `rspec`, and add them to version control.
* If there are default values, set them in migrations.
* Keep `db/initial_structure.sql` under version control.
* Use only one instance variable in each view.
* Validate the associated `belongs_to` object (`user`), not the database column
  (`user_id`).
* Use `db/seeds.rb` for data that is required in all environments.
* Prefer `cookies.signed` over `cookies` to [prevent tampering].
* Prefer `before_validation` hooks over overriding setters
  in order to avoid relying on the order of setters execution
* Opt-in to validation
* Avoid using Rails validation to enforce data consistency

[redirects]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.30
[Spring binstubs]: https://github.com/sstephenson/rbenv/wiki/Understanding-binstubs
[prevent tampering]: http://blog.bigbinary.com/2013/03/19/cookies-on-rails.html

Testing
-------

* Don't modify the environment with anything that persists after a test has finished.
* Don't make assertions on complex objects (including AR). This includes message assertions.
* Don't nest descriptions
* Don't rely on attributes set by FactoryGirl
* Avoid using FactoryGirl to build associated objects
* Avoid dynamic assertions where it isn't immediately obvious what the assertion is
* Avoid `any_instance` Prefer [dependency injection].
* Avoid `let!` in RSpec.
* Avoid using instance variables in tests.
* Avoid testing private methods.
* Avoid stubbing methods on the class you are testing.
* Avoid using VCR if there are other options
* Disable real HTTP requests to external services
* Test background jobs with a [`Delayed::Job` matcher].
* Use named `subject` in RSpec.
* Use an `it` example or test method for each execution path through the method.
* Use integration tests to execute the entire app.
* Add at least one acceptance test with Capybara following the happy path
  for every feature that adds or changes something in the UI.

[dependency injection]: http://en.wikipedia.org/wiki/Dependency_injection
[`Delayed::Job` matcher]: https://gist.github.com/3186463
[stubs and spies]: http://robots.thoughtbot.com/post/159805295/spy-vs-spy
[assertions about state]: https://speakerdeck.com/skmetz/magic-tricks-of-testing-railsconf?slide=51
[Fake]: http://robots.thoughtbot.com/post/219216005/fake-it
[SUT]: http://xunitpatterns.com/SUT.html

Bundler
-------

* Specify the [Ruby version] to be used on the project in the `Gemfile`.
* Use versionless `Gemfile` declarations for gems that have no know breaking changes in the latest version.
* Use exact versions in the `Gemfile` for fragile gems, such as Rails.

[Ruby version]: http://bundler.io/v1.3/gemfile_ruby.html

Postgres
--------

* Avoid multicolumn indexes in Postgres. It [combines multiple indexes]
  efficiently. Optimize later with a [compound index] if needed.
* Consider a [partial index] for queries on booleans.
* [Index foreign keys].

[combines multiple indexes]: http://www.postgresql.org/docs/9.1/static/indexes-bitmap-scans.html
[compound index]: http://www.postgresql.org/docs/9.2/static/indexes-bitmap-scans.html
[partial index]: http://www.postgresql.org/docs/9.1/static/indexes-partial.html
[Index foreign keys]: https://tomafro.net/2009/08/using-indexes-in-rails-index-your-associations

Background Jobs
---------------

* Use `.delay` for mailers, `Background` classes for every other DJ.

Email
-----

* Use a tool like [MailTrap] to look at each created or updated mailer view
  before merging.

[MailTrap]: https://mailtrap.io/

JavaScript
----------

* Use CoffeeScript.

jQuery
------

* When selecting DOM elements, only use CSS classes prefixed with `js_`. That way, when a developer sees such a class in the HTML source, they'll know exactly where to look to discover its purpose.
* Prefer [our dispatcher](https://coderwall.com/p/mhvucw) when js functionality has to be invoked on certain pages/controller actions instead of using `$(handler)`. [Example](https://gist.github.com/tadast/cb7618b26c7d5cd3d02a)

HTML
----

* Don't use a reset button for forms.

CSS
---

* Use Sass.

Sass
----

* Use `image-url` and `font-url`, not `url`, so the asset pipeline will re-write
  the correct paths to assets.

Browsers
--------

* Don't support clients without Javascript.
* Don't support IE6 or IE7.
