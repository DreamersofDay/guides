## Starting an Ember project

### Create

- Install Ember CLI `npm install -g ember-cli`
- Create your new app `ember new project-name`

### CircleCI

- Install ember-cli-alphasights by running `ember install:npm ember-cli-alphasights`
- Run the generator `ember generate alphasights`

### Deploy

- Install ember-cli-divshot by adding the following dependency to the package.json file
```
"ember-cli-divshot": "git+https://git@github.com/alphasights/ember-cli-divshot.git"
```
- Run `npm install && ember generate divshot --app=as-project-name` (note the `as-` prefix)
- Ask people belonging to the DevOps group to add the `DIVSHOT_TOKEN` env var to CircleCI
- Push to master
- Let the CI do the deployment to staging (ember-cli-alphasights takes care of that)
- Move the app from the devops+production@alphasights.com account to the AlphaSights organization on Divshot
- Promote to production when staging is ok with `dumbot divshot promote project-name`

Manual alternative:
- Deploy to staging via `ember divshot push staging`
- Promote from staging to production: `ember divshot promote staging production`
