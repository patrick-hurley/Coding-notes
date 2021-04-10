# Nuxt, Strapi, Postgres, REST

> **Versions** <br>
> Nuxt: 2.15.3 <br>
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