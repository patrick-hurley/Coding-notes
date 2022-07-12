# Docker

Containers that run images - each with their own OS, resources etc - each with a single task that can be scaled independently as needed.

For example, for a Vue.js, Express, Postgres fullstack app I could have 3 containers.

1. vue front-end
2. express api
3. postgres database

Docker Composer would then be used to 'orchestrate' these together.

I could also then add an API gateway as another container if I wish, or add the API container to Azure gateway.

I think you add these docker images to Azure (front end and back end to 'Azure Web Apps'), and then import the web app into APIM.

## Steps

### 1. Create a Dockerfile

Add a file called **Dockerfile** to the root.

The Dockerfile is a manifest that is used to create the Docker image

It contains;
* FROM - (the OS/platform)
* WORKDIR - (change the working directory - absolute path starting with /)
* RUN - (bash command)
* EXPOSE - (port)
* CMD - (executed with image is run)

```Dockerfile
# build stage
FROM node:14.16.0 as build-stage
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 2. Add a .dockerignore file

This will speed up the Docker build process as our local dependencies and git repo will not be sent ot the Docker daemon.

```ignore
    node_modules
    .git
    .gitignore
```

### 3. Create Docker image

Run `docker build -t [username]/[image-name] .`

So for example, `docker build -t pathurley/my-vue-app .`

The 'dot' at the end is intentional and represents the current directory.

We then validate the image has created using `docker images`, or look in the Docker Desktop app.

### 4. Start a container with the image we created

To start a container run `docker run -it -p [host port]:[container port] -d --name [container-name] [image-name]`

So,  `docker run -it -p [8081]:[8080] -d --name docker-vue-js my-vue-app`

-it  –  This flag sets the container in Interactive mode and allocate a Dedicated TTY id for later SSHing.

-d –  This flag sets the container to run in the background.

-p 8081:8080 – Port Forwarding Between Host and the Container. Left = host, Right = container. This exposes **8080** to other Docker containers on the same network (for inter container communication), and port **8081** to the host.


–name – Once started docker daemon assigns a random string name to the container. But i recommend defining a name can be handy way to add meaning to a container.

docker-vuejs – Name of the container we are starting ( Replacement of Container ID).

hanu4u89/docker-vuejs – The name of the image from which we are going to create a Container.





##Reference

This guy has created multiple docker images: [https://github.com/allanchua101/api-gateway-vue-express-pg](https://github.com/allanchua101/api-gateway-vue-express-pg)

