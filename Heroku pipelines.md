# Heroku pipelines

1. Create a new Heroku app for the environment

`heroku create my-app-staging --remote staging`

2. Add a new folder under strapi config, in the same way as production.

3. Update the heroku cli to default to this environment

`git config heroku.remote staging`

4. Add the postgres database

`heroku addons:create heroku-postgresql:hobby-dev`

5. Set the heroku configs for this environment

```sh
heroku config:set NODE_ENV=staging
heroku config:set HEROKU_URL=$(heroku info -s | grep web_url | cut -d= -f2)
```

6. Commit these changes to git, and then push to heroku

`git push staging master`


## Github integration

Set up Heroku to create review apps from pull requests. 

1. The developer creates a branch for their changes

2. They commit their changes, and then then create a pull request.

3. This automatically creates an app in Heroku 

4. If acceptable, I can merge this into the mainline

5. I set Heroku up to auto deploy this upto staging, which appears to then replace the staging app - the new code will then work with the staging database (I think)

6. I can then promote this version manually up to production.

* Alternatively, create a 'development' branch, and then have the auto-deploy set up so that any merges into 'development' get pushed to staging, and then if I merge development into 'main' it get auto-deployed up to production.