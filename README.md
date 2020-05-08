# Devops and Docker
## Part 1
### Exercise 1.1
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
8b1742459fcc        nginx               "nginx -g 'daemon of…"   39 seconds ago      Exited (0) 11 seconds ago                       naughty_poitras
68d8542759f3        nginx               "nginx -g 'daemon of…"   42 seconds ago      Exited (0) 3 seconds ago                        zealous_wilbur
e1e24b37a902        nginx               "nginx -g 'daemon of…"   46 seconds ago      Up 43 seconds               80/tcp              nervous_hellman

```
### Exercise 1.2
```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```
### Exercise 1.3
```
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"
```
### Exercise 1.4
```
$ docker run -d --rm -it devopsdockeruh/exec_bash_exercise
1920084f0091d538541fa7133aa58babd9a92821595d0311361ced4fa5c62ee3
[olli@shuntti ~]$ docker container ls -a
CONTAINER ID        IMAGE                               COMMAND             CREATED             STATUS              PORTS               NAMES
1920084f0091        devopsdockeruh/exec_bash_exercise   "node index"        18 seconds ago      Up 17 seconds                           friendly_saha
$ docker exec -it friendly_saha bash
root@1920084f0091:/usr/app# tail -f ./logs.txt
Mon, 13 Jan 2020 14:20:44 GMT
Mon, 13 Jan 2020 14:20:47 GMT
Secret message is:
"Docker is easy"
```
### Exercise 1.5
```
$ docker run -it ubuntu:latest sh -c 'apt update -y;apt install curl -y;echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;'
```
### Exercise 1.6
Dockerfile:
```
FROM devopsdockeruh/overwrite_cmd_exercise

CMD ["-c"]
```

Commands:
```
$ docker build -t docker-clock .
$ docker run docker-clock
```
### Exercise 1.7
Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt install curl -y
COPY script.sh .

CMD ["/usr/app/script.sh"]
```
script.sh:
```
#!/bin/sh
echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;
```

commands:
```
$ chmod +x script.sh 
$ docker build -t curler .
$ docker run -it curler
```

Output:
```
Input website:
helsinki.fi
Searching..
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="http://www.helsinki.fi/">here</a>.</p>
</body></html>
```
### Exercise 1.8
commands:
```
$ touch logs.txt
$ docker run -v ${PWD}/logs.txt:/usr/app/logs.txt devopsdockeruh/first_volume_exercise
```

Secret message:
```
Mon, 13 Jan 2020 18:34:15 GMT
Mon, 13 Jan 2020 18:34:18 GMT
Mon, 13 Jan 2020 18:34:21 GMT
Mon, 13 Jan 2020 18:34:24 GMT
Secret message is:
"Volume bind mount is easy"
```
### Exercise 1.9
commands:
```
$ docker run -p 80:80 devopsdockeruh/ports_exercise
```
### Exercise 1.10
Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install curl -y
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
COPY frontend-example-docker/ .
RUN npm install
RUN npm install -g serve
RUN npm run build

EXPOSE 5000

CMD ["serve", "-s", "-l", "5000", "dist"]
```
### Exercise 1.11
Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install curl -y
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
COPY backend-example-docker/ .
RUN npm install

EXPOSE 8000

CMD ["npm", "start"]
```

commands:
```
$ touch logs.txt
$ docker run -v ${PWD}/logs.txt:/usr/app/logs.txt -p 8000:8000 kurkkumopo-backend
```
### Exercise 1.12
Backend Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install curl -y
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
COPY backend-example-docker/ .
RUN npm install
EXPOSE 8000

ENV FRONT_URL=http://localhost:5000
CMD ["npm", "start"]
```
Frontend Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install curl -y
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
COPY frontend-example-docker/ .
ENV API_URL=http://localhost:8000
RUN npm install
RUN npm install -g serve
RUN npm run build

EXPOSE 5000

CMD ["serve", "-s", "-l", "5000", "dist"]
```

Commands:
```
$ docker build -t kurkku-frontti .
$ docker run -p 5000:5000 kurkku-frontti

$ docker build -t kurkku-bakki .
$ docker run -p 8000:8000 kurkku-bakki
```
### Exercise 1.13
Commands:
```
$ docker build -t java .
$ docker run -p 8080:8080 java
```

Dockerfile:
```
FROM openjdk:8

WORKDIR /usr/app/
COPY spring-example-project/ .
RUN ./mvnw package
EXPOSE 8080

CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```
### Exercise 1.14
Dockerfile:
```
FROM ruby:2.6.0
WORKDIR /usr/app/
COPY rails-example-project/ .
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
RUN gem install bundler
RUN bundle install
ENV SECRET_KEY_BASE secret
RUN rails db:migrate RAILS_ENV=production
RUN rake assets:precompile

EXPOSE 3000

ENV RAILS_LOG_TO_STDOUT=true

CMD ["rails", "s", "-e", "production"]
```

Commands:
```
$ docker build -t rails .
$ docker run -p 3000:3000 rails
```
### Exercise 1.15
DockerHub repo: https://hub.docker.com/repository/docker/ovatsia/sdxmenuv2

Dockerfile:
```
FROM python:3.7

WORKDIR /usr/app/
COPY ./ .

RUN pip install flask simplecache flask_scss
ENV FLASK_APP sodexomenuv2.py
ENV FLASK_ENV developement

EXPOSE 5000
CMD ["flask", "run", "--host=0.0.0.0"]
```
### Exercise 1.16
Application in heroku: https://oispakaljaaaaa.herokuapp.com/

### Exercise 1.17
Dockerfile:
```
FROM debian:latest

WORKDIR /usr/app
RUN apt update && apt upgrade

## NodeJS v12
RUN apt install -y curl python-pip
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt install -y nodejs

## ibmcloud CLI tool
RUN curl -sL https://public.dhe.ibm.com/cloud/bluemix/cli/bluemix-cli/0.21.0/IBM_Cloud_CLI_0.21.0_amd64.tar.gz | tar -xz
RUN ls -lha
RUN Bluemix_CLI/install
RUN rm -r Bluemix_CLI

## AWS CLI
RUN pip install awscli

ENTRYPOINT ["node"]
```

Docker image contains latest Debian-image with NodeJS 12.x.
Other components which are included is command line interfaces
for IBM Cloud / Bluemix and for Amazon Web Services. 

Container also runs node application when it is started. when
starting container, there should be bind mounted a vlume which
contains NodeJs -project. Location of main app file is specified
with last parameter at starting commands end.

Example:
`docker run -it -v ${PWD}/src:/usr/app/src/ nodenclouds /usr/app/src/app.js`

Command line interfaces to IBM Cloud / AWS are used as-is from 
command line.

Docker Hub repository: ovatsia/nodenclouds

## Part 2
Exercises for part 2

### Exercise 2.1
docker-compose.yml:
```
version: '3.5' 

services: 

  first_volume_exercise: 
    image: devopsdockeruh/first_volume_exercise
    volumes: 
    - ./logs.txt:/usr/app/logs.txt
    container_name: first_volume_exercise

```
### Exercise 2.2
docker-compose.yml:
```
version: '3.5' 

services: 

  app: 
    image: devopsdockeruh/ports_exercise
    ports: 
     - "80:80"
    container_name: ports_exercise

```
### Exercise 2.3
docker-compose.yml:
```
version: '3.5' 

services: 
  backend: 
    image: kurkku-backend
    build: ./back
    ports: 
     - "8000:8000"
    container_name: kurkku-backend
  frontend: 
    image: kurkku-frontend
    build: ./front
    ports: 
     - "5000:5000"
    container_name: kurkku-frontend

```
### Exercise 2.4
command: `docker-compose up --scale compute=2`
### Exercise 2.5
docker-compose.yml:
```
version: '3.5' 

services: 
  backend: 
    image: kurkku-backend
    build: ./back
    ports: 
     - "8000:8000"
    container_name: kurkku-backend
    environment:
     - REDIS=database
     - REDIS_PORT=6379
  frontend: 
    image: kurkku-frontend
    build: ./front
    ports: 
     - "5000:5000"
    container_name: kurkku-frontend
  database:
    image: redis:latest
    ports:
    - "6379:6379"
```
### Exercise 2.6
docker-compose.yml:
```
version: '3.5' 

services: 

  backend: 
    image: kurkku-backend
    build: ./back
    ports: 
     - "8000:8000"
    container_name: kurkku-backend
    environment:
    - REDIS=database
    - REDIS_PORT=6379
    - DB_USERNAME=juuseri
    - DB_PASSWORD=passu
    - DB_NAME=daattabaasi
    - DB_HOST=postgres
  frontend: 
    image: kurkku-frontend
    build: ./front
    ports: 
     - "5000:5000"
    container_name: kurkku-frontend
  database:
    image: redis:latest
    ports:
    - "6379:6379"
  postgres:
    image: postgres:latest
    ports:
    - "3306:3306"
    environment:
    - POSTGRES_PASSWORD=passu
    - POSTGRES_USER=juuseri
    - POSTGRES_DB=daattabaasi
```

### Exercise 2.7
docker-compose.yml:
```
version: '3.5' 

services:
  backend:
    build: ml-kurkkumopo-backend
    ports:
     - 5000:5000
    volumes:
     - data:/src/model
  frontend:
    build: ml-kurkkumopo-frontend
    ports:
     - 3000:3000
  training:
    build: ml-kurkkumopo-training
    volumes:
     - data:/src/model
     - images:/src/imgs
volumes:
  data:
  images:
```
### Exercise 2.8
nginx.conf:
```
events { worker_connections 1024; }

http {
  server {
    listen 80;

    location / {
      proxy_pass http://frontend:5000/;
    }

    location /api/ {
      proxy_pass http://backend:8000/;
    }
  }
}
```

docker-compose.yml:
```
version: '3.5' 

services: 
  proxy:
    image: nginx:latest
    ports: 
     - "80:80"
    volumes:
     - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
     - backend
     - frontend
  backend: 
    image: kurkku-backend
    build: ./back
    ports: 
     - "8000:8000"
    container_name: kurkku-backend
    environment:
     - DB_USERNAME=juuseri
     - DB_PASSWORD=passu
     - DB_NAME=daattabaasi
     - DB_HOST=postgres
     - FRONT_URL=http://localhost
  frontend: 
    image: kurkku-frontend
    build: ./front
    ports: 
     - "5000:5000"
    container_name: kurkku-frontend
    environment:
     - API_URL=http://localhost/api
  postgres:
    image: postgres:latest
    ports:
    - "3306:3306"
    environment:
    - POSTGRES_PASSWORD=passu
    - POSTGRES_USER=juuseri
    - POSTGRES_DB=daattabaasi
```
### Exercise 2.9
docker-compose.yml:
```
version: '3.5' 

services: 
    proxy:
      image: nginx:latest
      ports: 
       - "80:80"
      volumes:
       - ./nginx.conf:/etc/nginx/nginx.conf:ro
      depends_on:
       - backend
       - frontend
    backend: 
      image: kurkku-backend
      build: ./back
      ports: 
       - "8000:8000"
      container_name: kurkku-backend
      environment:
       - REDIS=database
       - REDIS_PORT=6379
       - DB_USERNAME=juuseri
       - DB_PASSWORD=passu
       - DB_NAME=daattabaasi
       - DB_HOST=postgres
       - FRONT_URL=http://localhost
      depends_on:
       - postgres
       - database
    frontend: 
      image: kurkku-frontend
      build: ./front
      ports: 
       - "5000:5000"
      container_name: kurkku-frontend
      environment:
        - API_URL=http://localhost/api
    postgres:
      image: postgres:latest
      ports:
      - "3306:3306"
      environment:
        - POSTGRES_PASSWORD=passu
        - POSTGRES_USER=juuseri
        - POSTGRES_DB=daattabaasi
      volumes:
       - ./database:/var/lib/postgresql/data
    database:
      image: redis:latest
      ports:
      - "6379:6379"
      volumes:
      - ./redis-data:/data
      command: ["redis-server", "--appendonly", "yes"]
```
### Exercise 2.10
docker-compose.yml is the same as in the previous exercise.

Backend Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install curl -y
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
COPY backend-example-docker/ .
RUN npm install
EXPOSE 8000

CMD ["npm", "start"]
```

Frontend Dockerfile:
```
FROM ubuntu:latest

WORKDIR /usr/app
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install curl -y
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt install -y nodejs
COPY frontend-example-docker/ .
ENV API_URL=/api
RUN npm install
RUN npm install -g serve
RUN npm run build

EXPOSE 5000

CMD ["serve", "-s", "-l", "5000", "dist"]
```

## Part 3
### Excercise 3.1
Before optimizing:
```
$ docker history kurkku-backend
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
aa0140370389        2 minutes ago       /bin/sh -c #(nop)  CMD ["npm" "start"]          0B
94cf323373db        2 minutes ago       /bin/sh -c #(nop)  ENV FRONT_URL=http://loca…   0B
bd9885f26f76        2 minutes ago       /bin/sh -c #(nop)  EXPOSE 8000                  0B
59575d5fd2f0        2 minutes ago       /bin/sh -c npm install                          58.4MB
665ea87280f4        2 minutes ago       /bin/sh -c #(nop) COPY dir:19a1ed09519035ff1…   581kB
3a5e3ca880f1        2 minutes ago       /bin/sh -c apt install -y nodejs                86.6MB
ba9bf964de6a        2 minutes ago       /bin/sh -c curl -sL https://deb.nodesource.c…   34.9MB
e34e4c1dbc6a        3 minutes ago       /bin/sh -c apt-get install curl -y              14.3MB
f6a539e4d95d        3 minutes ago       /bin/sh -c apt-get upgrade -y                   3.08MB
333594e0c625        4 minutes ago       /bin/sh -c apt-get update                       27.8MB
02d8476cc04b        4 minutes ago       /bin/sh -c #(nop) WORKDIR /usr/app              0B
549b9b86cb8d        3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           3 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           3 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
<missing>           3 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     987kB
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:53f100793e6c0adfc…   63.2MB
TOTAL: 290MB

$ docker history kurkku-frontend
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
29b7a2d6c09d        2 minutes ago       /bin/sh -c #(nop)  CMD ["serve" "-s" "-l" "5…   0B
d0f45ff7f12f        2 minutes ago       /bin/sh -c #(nop)  EXPOSE 5000                  0B
538c331fd750        2 minutes ago       /bin/sh -c npm run build                        26.9MB
2bd70b9dae9a        2 minutes ago       /bin/sh -c npm install -g serve                 5.45MB
add555956ae8        2 minutes ago       /bin/sh -c npm install                          154MB
2022cca69b1b        3 minutes ago       /bin/sh -c #(nop)  ENV API_URL=http://localh…   0B
e54f5fcfe180        3 minutes ago       /bin/sh -c #(nop) COPY dir:45abe52d3482a1179…   574kB
3a5e3ca880f1        3 minutes ago       /bin/sh -c apt install -y nodejs                86.6MB
ba9bf964de6a        4 minutes ago       /bin/sh -c curl -sL https://deb.nodesource.c…   34.9MB
e34e4c1dbc6a        4 minutes ago       /bin/sh -c apt-get install curl -y              14.3MB
f6a539e4d95d        5 minutes ago       /bin/sh -c apt-get upgrade -y                   3.08MB
333594e0c625        5 minutes ago       /bin/sh -c apt-get update                       27.8MB
02d8476cc04b        5 minutes ago       /bin/sh -c #(nop) WORKDIR /usr/app              0B
549b9b86cb8d        3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           3 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           3 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
<missing>           3 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     987kB
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:53f100793e6c0adfc…   63.2MB
TOTAL: 418MB
```

After optimizing:
```
$ docker history kurkku-backend
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
fafd21d4dfba        2 minutes ago       /bin/sh -c #(nop)  CMD ["npm" "start"]          0B
5c737ad1c0ad        2 minutes ago       /bin/sh -c #(nop)  ENV FRONT_URL=http://loca…   0B
56d59ebf8742        2 minutes ago       /bin/sh -c #(nop)  EXPOSE 8000                  0B
08f1fdbc0148        2 minutes ago       /bin/sh -c apt-get update &&     apt-get ins…   190MB
e619d632b47e        9 minutes ago       /bin/sh -c #(nop) COPY dir:ab4ea87b075f7d6c8…   581kB
257313d58660        9 minutes ago       /bin/sh -c #(nop) WORKDIR /app                  0B
549b9b86cb8d        3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           3 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           3 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
<missing>           3 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     987kB
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:53f100793e6c0adfc…   63.2MB
TOTAL: 255MB

$ docker history kurkku-frontend
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
a420cad27972        23 seconds ago      /bin/sh -c #(nop)  CMD ["serve" "-s" "-l" "5…   0B
32d00c5d7d3d        25 seconds ago      /bin/sh -c #(nop)  EXPOSE 5000                  0B
80127b07a454        30 seconds ago      /bin/sh -c apt-get update && apt-get install…   318MB
cb746cabd9ef        2 minutes ago       /bin/sh -c #(nop)  ENV API_URL=http://localh…   0B
e8afda9b790a        2 minutes ago       /bin/sh -c #(nop) COPY dir:e4de8effde8253d65…   574kB
257313d58660        17 minutes ago      /bin/sh -c #(nop) WORKDIR /app                  0B
549b9b86cb8d        3 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           3 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           3 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
<missing>           3 weeks ago         /bin/sh -c [ -z "$(apt-get indextargets)" ]     987kB
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:53f100793e6c0adfc…   63.2MB
TOTAL: 383MB
```

### Excercise 3.2
### Excercise 3.3
### Excercise 3.4
### Excercise 3.5
### Excercise 3.6
### Excercise 3.7