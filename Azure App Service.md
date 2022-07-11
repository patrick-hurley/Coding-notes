# Azure App Service

**References**
[https://azure.microsoft.com/en-gb/services/app-service/#features](https://azure.microsoft.com/en-gb/services/app-service/#features)

From my current understanding, 'Azure App Service' is similar to Heroku in that it allows us to host a static client, and run a node js server - covering auto-scaling, load balancing, CI/CD.

We also need to: 

- Create a Docker image of our app before deploying (I think) - this is something Heroku does for us as part of the buildpack.

- Add the Postgres database using [Azure Database for PostgreSQL](https://azure.microsoft.com/en-gb/services/postgresql/)

- Add APIM (The main reason we're doing this) 


##Vs Heroku

Heroku is going to be a lot less to manage - we just add the postgres database as an 'addon', and the docker (containerization) step appears to included.

But once we have that, we're also be able to rate limit which we can't do in Heroku, so would need to implement manually.

We don't have APIM in Heroku.

Apparently Heroku can get very expensive if you need the higher tiers. Main worry is that we go for Heroku for ease-of-use then inevitably have to migrate to Azure.

##Notes

I think you have 2 options for hosting the API - either using Docker, or using NodeJs runtime directly on Azure. 


Tutorial using NodeJS runtime: [https://medium.com/bb-tutorials-and-thoughts/how-to-build-and-deploy-pean-stack-on-azure-app-services-2baf60c1e091](https://medium.com/bb-tutorials-and-thoughts/how-to-build-and-deploy-pean-stack-on-azure-app-services-2baf60c1e091)

Tutorial using Docker runtime: [https://medium.com/bb-tutorials-and-thoughts/how-to-build-and-deploy-pean-stack-on-azure-app-services-82caccc615b9](https://medium.com/bb-tutorials-and-thoughts/how-to-build-and-deploy-pean-stack-on-azure-app-services-82caccc615b9) 

