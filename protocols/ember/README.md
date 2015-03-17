## Starting an Ember project

### Create

- Install Ember CLI `npm install -g ember-cli`
- Create your new app `ember new project-name`

### CircleCI

- Install ember-cli-alphasights by adding the following dependency to the package.json
```
"ember-cli-alphasights": "git+ssh://git@github.com/alphasights/ember-cli-alphasights.git"
```
- Run `npm install && ember generate alphasights`
- Remove `ember-cli-alphasights` from your package.json

### Deploy

- Install ember-cli-divshot by adding the following dependency to the package.json file
```
"ember-cli-divshot": "git+https://git@github.com/matteodepalo/ember-cli-divshot.git"
```
- Run `npm install && ember generate divshot`
- Rename the app name in the divshot.json file to `as-project-name`

```
{
  "name": "as-phoenix",
  "root": "./dist",
  "routes": {
    "/tests": "tests/index.html",
    "/tests/**": "tests/index.html",
    "/**": "index.html"
  }
}
```

- Ask people belonging to the DevOps group to add the `DIVSHOT_TOKEN` env var to CircleCI
- Push to master
- Let the CI do the deployment to staging (ember-cli-alphasights takes care of that)
- Promote to production when staging is ok with `dumbot divshot promote project-name`

Manual alternative:
- Deploy to staging via `ember divshot push staging`
- Promote from staging to production: `ember divshot promote staging production`
