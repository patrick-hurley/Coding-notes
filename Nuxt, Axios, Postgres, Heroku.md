# Nuxt, Axios, Postgres, Heroku

> **Versions** <br>
> Nuxt: 2.15.3 <br>
> @nuxt/axios: 5.13.1 <br>
> Strapi: 3.5.4

## Setup

**frontend**
`npx create-nuxt-app frontend`
`npm install --save-dev sass sass-loader@10 fibers`

**backend**

Firstly we need to create a `Postgres` database in `pgAdmin4`.

1. Create a new user role will superuser permissions.
2. Create a new database, and assign this user.
3. Run `npx create-strapi-app api`
4. Choose the manual installation option from the cli, and select `postgres`, connecting to the new database.
5. Run `npm run develop` and create a user.
6. Strapi is now installed on postgres. Simple!

## Axios and Nuxt

Update `config.nuxt.js`

```javascript
publicRuntimeConfig: {
    axios: {
        baseURL: process.env.BACKEND_URL || 'http://localhost:1337',
    },
}
```

To use Nuxt static generation with `axios`, this only appears to work when the `axios` call is in an `asyncData` method, and not in `mounted()`

```javascript
// index.vue
export default {
    async asyncData({ $axios }) {
    const articles = await $axios.$get('articles')
        return { articles }
    },
}
```

```javascript
// _slug.vue
export default {
    async asyncData({ $axios, params }) {
    const article = await $axios.$get(`articles/${params.id}`)
        return { article }
    },
}
```

## Host Strapi on Heroku

The full docs on how to deploy Strapi to Heroku are [here](https://strapi.io/documentation/developer-docs/latest/setup-deployment-guides/deployment/hosting-guides/heroku.html), but here is a summary on the steps.

1. Initialise a `git` repo in the Strapi project folder.

2. Use `heroku create` to create a Heroku project with a random name, or `heroku create `my-app-name` for a custom one.
> This will create a project on Heroku, and add a `git remote` location called `heroku` pointing to it.

3. Use the following cli command to create a postgres database for our project on Heroku:
```sh
heroku addons:create heroku-postgresql:hobby-dev
```

4. Retrieve the database credentials with `heroku config`, however this is automatically exposed to our app by the add-on.

5. Install the following package so that strapi can deconstuct this string if the address changes on Heroku:
```sh
npm install pg-connection-string --save
```

6. Create two files in a new folder of `./config/env/production/`,
    * `database.js`
    ```javascript
    const parse = require('pg-connection-string').parse;
    const config = parse(process.env.DATABASE_URL);

    module.exports = ({ env }) => ({
        defaultConnection: 'default',
        connections: {
            default: {
                connector: 'bookshelf',
                settings: {
                    client: 'postgres',
                    host: config.host,
                    port: config.port,
                    database: config.database,
                    username: config.user,
                    password: config.password,
                    ssl: {
                        rejectUnauthorized: false,
                    },
                },
                options: {
                    ssl: true,
                },
            },
        },
    });
    ```

    * `server.js`
    ```javascript
    module.exports = ({ env }) => ({
        url: env('HEROKU_URL'),
    });
    ```

    7. Update heroku config settings:
    ```sh
    heroku config:set NODE_ENV=production
    heroku config:set HEROKU_URL=$(heroku info -s | grep web_url | cut -d= -f2)
    ```

    8. Commit changes and push to Heroku:
    ```sh
    git add .
    git commit -m "Updated database config"
    ```
    ```sh
    git push heroku HEAD:main
    ```
    9. Open the project on Heroku with `heroku open`

    ### Install Strapi

    We now have all of the files we need on Heroku, a new database provisioned, and the database config setup for production.

    To install Strapi, go to `[myapp.com]/admin` and follow in the installation steps.

    > `Content-Types Builder` plugin is disabled in production, so any changes need to be made locally, committed in git, and then pushed up to Heroku.
