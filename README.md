task1
docker –- version
docker version
docker info

docker pull hello-world
docker pull Ubuntu
docker pull nginx
docker images

docker run hello-world
docker run -it --name myubuntu ubuntu bash

docker ps –a
docker start myubuntu
docker exec -it myubuntu bash
exit
docker restart myubuntu
docker inspect myubuntu
docker stop myubuntu
docker rm myubuntu

docker run -d -p 8080:80 --name mynginx nginx
docker port mynginx
docker logs mynginx
	open chrome http://localhost:8080
docker stop mynginx
docker rm mynginx

create a folder and 2 files inside the folder
1. Index.html
2. Docker file-> with no extension
Include this line in dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/index.html

docker build --no-cache -t myimage .
docker images
docker run -d -p 9091:80 --name mysite1 myimage
         http://localhost:9091
	docker stop mysite1
	docker rm mysite1

docker login
docker tag myimage <username>/myimage
docker push <username>/myimage

docker rmi <username>/myimage
docker pull <username>/myimage
docker run -p 9091:80 <username>/myimage
http://localhost:9091






task2
package.json
{
  "name": "simple-docker-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.19.2"
  }
}


index.js
const express = require('express');
const app = express();
app.get('/', (req, res) => {
  res.send('Hello, Docker!');
});
app.listen(3000);


dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]

cmd
docker build -t my-simple-app:v1 .
docker run -p 8080:3000 my-simple-app:v1







task3
package.json
{
  "name": "compose-demo",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "express": "^4.19.2",
    "redis": "^4.6.13"
  }
}

index.js
const express = require('express');
const redis = require('redis');
const app = express();
const client = redis.createClient({ url: 'redis://redis-db:6379' });

async function startApp() {
    await client.connect();
    app.get('/', async (req, res) => {
        const visits = await client.incr('visits');
        res.send(`Page views: ${visits}`);
    });
    app.listen(3000);
}
startApp();


dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]


docker-compose.yml
version: '3.8'
services:
  web-app:
    build: .
    ports:
      - "8080:3000"
    depends_on:
      - redis-db
  redis-db:
    image: "redis:alpine"


cmd
docker compose up

task5
kubectl create deployment my-nginx --image=nginx
kubectl expose deployment my-nginx --port=80 --type=NodePort
kubectl get services
