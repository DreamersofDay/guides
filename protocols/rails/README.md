Rails Protocol
==============

A guide for writing great web apps.

Set Up Laptop
-------------

Set up your laptop with boxen.

Create App
----------

Create Rails app

    rails new app

Create GitHub repo and set it as remote

    git remote add origin git@github.com:organization/app.git

Push it
  
    git push -u origin master

Create staging Heroku app

    heroku apps:create js-app-staging

Create production Heroku app
  
    heroku apps:create js-app-production

Set Up App
----------

Get `.env` staging vars from the vault.

Git Protocol
------------

Follow the normal [Git Protocol](/protocol/git).

Setup a webhook that points to an installation of `https://github.com/alphasights/lion-api`
and that triggers only on Pull Request events.

Deploy
------

Wait for the CI to test your feature branch.

Merge from GitHub.

Wait for the CI to test the master branch.

View the list of new commits.
  
  heroku pipeline:diff -a js-app-staging

If necessary, add new environment variables to staging and production.

    heroku config:add NEW_VARIABLE=value -a js-app-staging
    heroku config:add NEW_VARIABLE=value -a js-app-production

Promote to production

    heroku pipeline:promote -a js-app-staging

If necessary, run migrations and restart the dynos.

    heroku run rake db:migrate -a js-app-production
    heroku restart -a js-app-production

Test the feature in browser.

Watch logs and metrics dashboards.

Set Up Production Environment
-----------------------------

* Make sure that your [`Procfile`] is set up to run Puma.
* Make sure the PG Backups add-on is enabled.

[`Procfile`]: https://devcenter.heroku.com/articles/procfile
