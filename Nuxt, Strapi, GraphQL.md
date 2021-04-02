# Nuxt, Strapi, GraphQL

## Setup

**frontend**
`npx create-nuxt-app frontend`

**backend**
`npx create-strapi-app api`

---

## Defining content in Strapi

-   Add a new collection.
-   Add all fields needed for this collection (eg, Article).
-   When saved the server will restart and make 'Article' available from the menu bar.

## Adding content

-   Open the collection and create a new record.
-   When saved the article will be in draft form.
-   Publish to make it available.

## Permission

So a user can access the content via an API you need to configure this.

-   Go to **General &rarr; Settings &rarr; U/P Plugin &rarr; Roles**
    -   Authenticated - uses a JWT.
    -   Public - is unauthenticated.

Using public, select **find** and **find one** to allow GET requests to our Article collection (all records, and a single).

## Test the API

We can now open up Postman and test the API using http://localhost:1337/articles.

## GraphQL

For Strapi this can either be installed via the Marketplace plugin, or on the command line.

`npx strapi install graphql`

After installing on command line, just restart the Strapi server and the GraphQL plugin will show as installed.

Access the GraphQL playgound UI at http://localhost:1337/graphql

```graphql
query {
    articles {
        title
        date
    }
}
```

This return a JSON response from our Articles table, containing the title and date for all articles.

---

## Progress

We have now setup;

-   Nuxt frontend
-   Strapi backend
-   GraphQL working with Strapi

Next we need to connect Nuxt to Strapi so we can make easily make GraphQL API calls.

The following endpoints are now available:

-   /graphql (POST)

-   /articles (GET)

---

## Connecting Nuxt to Strapi

```ssh
cd frontend
npm install @nuxtjs/apollo graphql graphql-tag
```

### 1. Update `nuxt.config.js`

```javascript
modules: [
    '@nuxtjs/apollo'
],

apollo: {
    clientConfigs: {
        default: {
            httpEndpoint: process.env.BACKEND_URL || "http://localhost:1337/graphgl"
        }
    }
}
```

### 2. Create `queries.js`

Add a new directory called `graphql` inside `frontend`, with a file called `queries.js`.

```javascript
import gql from "graphql-tag";

export const allArticlesQuery = gql`
// GraphQL query for all articles
query {
    articles {
        title
        date
    }
}
`;

export const singleArticleQuery = gql`
// GraphQL query for single article
query {
    article(id:1){
        title
        date
    }
}
`;
```

### 3. Update `index.vue`

```javascript
import { allArticlesQuery } from '@/graphql/queries.js'

data() {
    return {
        articles: []
    }
},
apollo: {
    articles: {
        prefetch: true,
        query: allArticlesQuery
    }
}
```

---
## Creating dynamic pages

 `index.vue` can now be updated to have a `v-for` list to loop though all for the content received from the **Articles** `graphQL` call.

 ```javascript
<div
    v-for="article in articles"
    :key="article.id"
    class="section">
    
    <NuxtLink
        :to="{ path: article.slug, query: { id: article.id } }"
        >{{ article.title }}
    </NuxtLink>
</div>
 ```

With Nuxt.js, you don't need to maintain a `route.js` file, and instead just create a vue file for each route in the `pages` folder. `route.js` is then dynamically generated during the build process.

For dynamic content, start the filename with an underscore such as `_myslug.vue`, and in this case I'm using `_id.vue`.

### Make the GraphQL call dynamic

Our `graphql` call for a single article is currently harcoded to **'id = 1'** and instead we want this to be dependent on the url paramater of `id`.

#### 1. Update `queries.js`

```javascript
export const singleArticleQuery = gql`
    query singleArticleQuery($id: ID!) {
        article(id: $id) {
            id
            title
            date
            body
            description
            slug
        }
    }
`
```

#### 2. Create `_id.vue`
```javascript
import { singleArticleQuery } from '@/graphql/queries'
export default {
    data() {
        return {
            // this caused issues if set to null,
            // and would only work on page reload
            article: [],
        }
    },
    apollo: {
        article: {
            prefetch: true,
            query: singleArticleQuery,
            variables() {
                return {
                    // this is the id from the url
                    id: this.$route.query.id,
                }
            },
        },
    },
}
```



