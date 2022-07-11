# Docker

Containers that run images - each with their own OS, resources etc - each with a single task that can be scaled independently as needed.

For example, for a Vue.js, Express, Postgres fullstack app I could have 3 containers.

1. vue front-end
2. express api
3. postgres database

Docker Composer would then be used to 'orchestrate' these together.

I could also then add an API gateway as another container if I wish, or add the API container to Azure gateway.

I think you add these docker images to Azure (front end and back end to 'Azure Web Apps'), and then import the web app into APIM.

##Referecne

This guy has created multiple docker images: [https://github.com/allanchua101/api-gateway-vue-express-pg](https://github.com/allanchua101/api-gateway-vue-express-pg)

