Docker For Developer commands for working with docker
----


### First Hello world

```shell
docker run hello-world
```


### Docker commands

```shell
~ ➤ docker run busybox echo hi there himanshu
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
61c5ed1cbdf8: Pull complete
Digest: sha256:4f47c01fa91355af2865ac10fef5bf6ec9c7f42ad2321377c21e844427972977
Status: Downloaded newer image for busybox:latest
hi there himanshu
```



### Variation in command

```shell
~ ➤ docker run busybox ls
bin
dev
etc
home
proc
root
sys
tmp
usr
var
```


### Checking number of running container

```shell
~ ➤ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
# As no container is running currently
# Whatever commands we were running it was running and after run it was closing down immediately
```


### All container run in history

```shell
~ ➤ docker ps --all
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                          PORTS               NAMES
62b050fb4929        busybox             "ping google.com"        About a minute ago   Exited (1) About a minute ago                       tender_elbakyan
ee91d77b9cc1        busybox             "ping https://www.go…"   2 minutes ago        Exited (1) 2 minutes ago                            clever_gauss
0a47f5f42a27        busybox             "ping https://www.go…"   2 minutes ago        Exited (1) 2 minutes ago                            jovial_raman
3d2f401bb5e0        busybox             "ping google.com"        10 minutes ago       Exited (1) 6 minutes ago                            vigorous_cray
2cac8b65ac03        busybox             "ls"                     23 minutes ago       Exited (0) 23 minutes ago                           confident_curran
ca932e11bb76        busybox             "echo how r u"           24 minutes ago       Exited (0) 24 minutes ago                           elated_maxwell
daa808487ad0        busybox             "echo hi there himan…"   26 minutes ago       Exited (0) 26 minutes ago                           gracious_khorana
e6719cfd50e2        hello-world         "/hello"                 48 minutes ago       Exited (0) 48 minutes ago                           festive_payne
```

docker run = docker create + docker start
so when we execute command `docker run buysbox echo hi there` it create the container using image and start it at the same time

Now let's start understanding with step by step process


```sh
~ ➤ docker create hello-world
0b5b290c76d9cc6a667e97c551b55cf7d2e12f16f1ec27bc4e0f661ec2b193af // This is the ID of container we just created
```

Now let's run


```sh
~ ➤ docker start -a 0b5b290c76d9cc6a667e97c551b55cf7d2e12f16f1ec27bc4e0f661ec2b193af // -a comand is going to watch what running inside the container and going to print on console for you

Hello from Docker!
```

Removing stopped container from the log


```sh
docker system prune
```


### Retrieve Logs

```shell
~ ➤ docker create busybox echo hi there
43703870d88a8e5ae847902beef7544a1d83c860b940685ec9d4c8b693826902
~ ➤ docker start 43703870d88a8e5ae847902beef7544a1d83c860b940685ec9d4c8b693826902
43703870d88a8e5ae847902beef7544a1d83c860b940685ec9d4c8b693826902
~ ➤ docker logs 43703870d88a8e5ae847902beef7544a1d83c860b940685ec9d4c8b693826902
hi there
~ ➤

```



### Stopping or Killing command

```shell
docker stop 7b45fafb103f0a5c7870faa80500191efa1ae46a70ad5f11bf6038e2b8302afe
# send "SIGTERM" signal and wait to shutdown
# When issue docker stop command if it is not able to stop in  10 sedond then docker issue kill signal to shutdown the container

docker kill 7b45fafb103f0a5c7870faa80500191efa1ae46a70ad5f11bf6038e2b8302afe
# send "SIGKILL" shutdown right now without doing any shutdown process
```


### Working with redis

```shell
docker run redis
```

Running redis inside container

```sh
~ ➤ docker ps -it 0ef2bef64d66 redis-cli
unknown shorthand flag: 'i' in -it
See 'docker ps --help'.
~ ➤ docker exec -it 0ef2bef64d66 redis-cli
127.0.0.1:6379> set myvalue 6
OK
127.0.0.1:6379> get myvalue
"6"
```



### Open a shell in the context of your running container

```shell
~ ➤ docker exec -it 0ef2bef64d66 sh // sh is program that execute inside the container
# ls
# ls -a
.  ..
# cd ~/
# ls
# cd /
# ls
bin   data  etc   lib	 media	opt   root  sbin  sys  usr
boot  dev   home  lib64  mnt	proc  run   srv   tmp  var
# echo hi there
hi there
# export b-5
sh: 8: export: b-5: bad variable name
# export b=5
# echo $b
5
# redis-cli
127.0.0.1:6379>
#
```


### Alternate of running docker run with shell attached

```shell
~ ➤ docker run -it busybox sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # cd ~/
~ # ls
~ # cd /
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # figlet hello
sh: figlet: not found
/ # ping google.com
PING google.com (172.217.166.46): 56 data bytes
^C
--- google.com ping statistics ---
11 packets transmitted, 0 packets received, 100% packet loss
/ # echo Himanshu
Himanshu
/ #
~ ➤
```

## Creating a docker image using a dockerfile

Flow to create a docker file

1. Specify a base image
2. Run Some commands to install additional program
3. Specify a command to run on container startup

![Docker File flow](./images/docker-flow.PNG)


### Create an image that runs redis-server from scratch

### Sample Code of creating a docker file


```Dockerfile
# Use an existing docker image as a base
FROM alpine 

# Download and install a dependency
RUN apk add --update redis

#Tell the image what to do when it starts as container
CMD ["redis-server"]
```

Now try to execute and see the output


```sh
redis-image ➤ docker build .
Sending build context to Docker daemon  2.048kB
Step 1/3 : FROM alpine
latest: Pulling from library/alpine
df20fa9351a1: Already exists
Digest: sha256:185518070891758909c9f839cf4ca393ee977ac378609f700f60a771a2dfe321
Status: Downloaded newer image for alpine:latest
 ---> a24bb4013296
Step 2/3 : RUN apk add --update redis
 ---> Running in 8721fbda27f4
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/1) Installing redis (5.0.9-r0)
Executing redis-5.0.9-r0.pre-install
Executing redis-5.0.9-r0.post-install
Executing busybox-1.31.1-r16.trigger
OK: 7 MiB in 15 packages
Removing intermediate container 8721fbda27f4
 ---> 82551894e636
Step 3/3 : CMD ["redis-server"]
 ---> Running in f028ad5d4186
Removing intermediate container f028ad5d4186
 ---> 5c2692570bf8
Successfully built 5c2692570bf8
redis-image ➤ docker run 5c2692570bf8
1:C 23 Aug 2020 12:19:40.705 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 23 Aug 2020 12:19:40.705 # Redis version=5.0.9, bits=64, commit=869dcbdc, modified=0, pid=1, just started
1:C 23 Aug 2020 12:19:40.705 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 23 Aug 2020 12:19:40.707 * Running mode=standalone, port=6379.
1:M 23 Aug 2020 12:19:40.707 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:M 23 Aug 2020 12:19:40.707 # Server initialized
1:M 23 Aug 2020 12:19:40.708 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1:M 23 Aug 2020 12:19:40.708 * Ready to accept connections
```

Now lets go inside docker and execute command `redis-cli`


```sh
redis-image ➤ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
c78e09c7dc04        5c2692570bf8        "redis-server"      2 minutes ago       Up 2 minutes                            zen_hamilton
redis-image ➤ docker exec -it c78e09c7dc04 sh
/ # redis-cli
127.0.0.1:6379>
```

Now let's understand what happened inside the dockerfile when we built it

![Docker Build flow](./images/docker-build.PNG)


## Running a nodejs application in docker

![Node Application With Docker](./images/node-application-docker.PNG)


### Creating a docker file

```dockerfile
# Specify a base image
# :alpine contains compact image minimum required for running the solution
# the node image contains complete environment with file system to run a node application including npm, node 
FROM node:alpine

# Copy files we need to copy files as container won't be having them so we need to copy them using copy command

COPY ./ ./

# Install some dependencies
RUN npm install

# Default command

CMD ["npm", "start"]
```

Now let's create our node application


### Creating a `package.json` file


```js
{
  "dependencies": {
    "express": "4.17.1"
  },
  "scripts": {
    "start": "node index.js"
  }
}

```

### Creating a server file using js `index.js`

It's just creating a simple HTTP server and when requested given a text *`Hi There`*

```js
const express = require("express")

const app = express();

app.get('/', (req, res) => {
  res.send("Hi there");
});

app.listen(8000, () => {
  console.log('Listening on port 8000')
})
```

### Building your image with above configuration


```sh
docker build . // Don't forget the . at the end it set the context
# After building image it will give an id which will be used further for running the container
```

### Tagging the image while building 

command as below 

```sh
docker build -t himanshu/simpleweb . // Again don't forget about the . at the end
Sending build context to Docker daemon  4.096kB
Step 1/4 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/4 : COPY ./ ./
 ---> Using cache
 ---> a6722dcbf3eb
Step 3/4 : RUN npm install
 ---> Using cache
 ---> 347fb971b8ce
Step 4/4 : CMD ["npm", "start"]
 ---> Using cache
 ---> c3a4ddd14b84
Successfully built c3a4ddd14b84
Successfully tagged himanshu/simpleweb:latest
```

Now we can run the container by issueing run command

```sh
docker run himanshu/simpleweb

> @ start /
> node index.js

Listening on port 8000
```

But still we won't able to access the solution from our local machine as it is running inside the container

To access it from outside of container we need to do port mapping (Port Forwarding)

![Port Mapping](./images/port-mapping.PNG)

*Port forwarding is runtime constraints which means it only changes when the solution is running*

![Port Forwarding](./images/port-forwarding.PNG)


Now let's run with port forwarding


```sh
simpleweb ➤ docker run -p 8000:8000 himanshu/simpleweb

> @ start /
> node index.js

Listening on port 8000
```

Now if i'll open the browser in my local machine and navigate to url `localhost:8000`

![Port Forwarding Output](./images/port-forwarding-output.PNG)

We can not copy files at the root directory as it can make conflict with other folder or files.
So we need to create a working directory where we can put file and execute them from there

![Create Directory inside container](./images/create-directory-in-container.PNG)

Now let's update our `Docker` file and try to set the working directory as `/usr/home`


```dockerfile
# Specify a base image
FROM node:alpine

# Creating Workdir

WORKDIR /usr/app // This is where we have created our working directory

# Copy files 

COPY ./ ./

# Install some dependencies
RUN npm install

# Default command

CMD ["npm", "start"]
```

Let's build our image again


```sh
simpleweb ➤ docker build -t himanshu/simpleweb .
Sending build context to Docker daemon  4.096kB
Step 1/5 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/5 : WORKDIR /usr/app
 ---> Running in 6a995d19c9e8
Removing intermediate container 6a995d19c9e8
 ---> b7a0314470ca
Step 3/5 : COPY ./ ./
 ---> 64a6919aaa0a
Step 4/5 : RUN npm install
 ---> Running in a09be04288a0
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN app No description
npm WARN app No repository field.
npm WARN app No license field.

added 50 packages from 37 contributors and audited 50 packages in 2.882s
found 0 vulnerabilities

Removing intermediate container a09be04288a0
 ---> 3d0bc2c0cf06
Step 5/5 : CMD ["npm", "start"]
 ---> Running in 980d715134f9
Removing intermediate container 980d715134f9
 ---> 6a70096c2af4
Successfully built 6a70096c2af4
Successfully tagged himanshu/simpleweb:latest
```

Now run the solution again and see the output with port mapping

```sh
simpleweb ➤ docker run -p 8000:8000 himanshu/simpleweb

> @ start /usr/app
> node index.js

Listening on port 8000
```

Open the browser in your local machine and navigate to URL `localhost:8000`
As we can see our output didn't change


![Port Forwarding Output](./images/port-forwarding-output.PNG)


Now let's see the file structure inside the container


```sh
simpleweb ➤ docker exec -it 8fc7b2010c74 sh
/usr/app # ls
Dockerfile         node_modules       package.json index.js           package-lock.json // As we can observe all the files created inside the /usr/app directory and when try to attach to container we directly taken to working directory
/usr/app # cd /
/ # ls
bin    home   mnt    root   srv    usr dev    lib    opt    run    sys    var etc    media  proc   sbin   tmp // Here there is no change as we have set the working directory different than root directory
/ #
```

When we change our file of source code we need to re-build our solution and re-run the container

If we want to make change and it should sync directly with container then we need to do some configuration


### Avoiding re-running the npm install or installing dependencies if we make small changes to our source file and not in dependencies

Now let's update our dockerfile to accomodate those changes



```dockerfile
# Specify a base image
FROM node:alpine

# Creating Workdir

WORKDIR /usr/app

# To prevent building the dependency again here we will copy package.json file first if there will be any change it will run `npm install` if no change it won't run
COPY ./package.json ./  

# Install some dependencies
RUN npm install

# After the run of npm install we can copy other files 
COPY ./ ./
# Default command

CMD ["npm", "start"]
```

Now let's build again and run the solution

```sh
simpleweb ➤ docker build -t himanshu/simpleweb .
Sending build context to Docker daemon  4.096kB
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR /usr/app
 ---> Using cache
 ---> b7a0314470ca
Step 3/6 : COPY ./package.json ./
 ---> Using cache
 ---> aa64fb31b5a6
Step 4/6 : RUN npm install
 ---> Using cache
 ---> 9126ecf83db2
Step 5/6 : COPY ./ ./
 ---> 4be935743ef7
Step 6/6 : CMD ["npm", "start"]
 ---> Running in 6ca22a0c4faf
Removing intermediate container 6ca22a0c4faf
 ---> 6bfc66733f12
Successfully built 6bfc66733f12
Successfully tagged himanshu/simpleweb:latest
```

It ran very fast as expected as it taking dependencies from cache




## Let's create a realtime project where a node server will connect to a redis server and get the data back

For this let's create a new folder called `visits`

1. Create a `package.json` file

```js
{
  "dependencies": {
    "express": "4.17.1",
    "redis": "3.0.2"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```

2. Creeate a `index.js` file

```js
const express = require('express');

const redis = require('redis');

cost app = express();
const client = redis.createClient();
client.set('visits', 0);

app.get('/', (req, res) => {
  client.get('visits', (err, visits) => {
    res.send('Number of visits is ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('listening on port 8081')
});
```

3. Now let's create a docker file 

```dockerfile
FROM node:alpine

WORKDIR '/app'

COPY package.json .

RUN npm install

COPY . .

CMD ["npm", "start"]
```

Now lets build the image with tag name

```sh
visits ➤ docker build -t himanshu/visits:latest .
Sending build context to Docker daemon  4.096kB
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 20f822e189ea
Step 4/6 : RUN npm install
 ---> Using cache
 ---> b1356dab35d1
Step 5/6 : COPY . .
 ---> Using cache
 ---> cdb43262dad8
Step 6/6 : CMD ["npm", "start"]
 ---> Using cache
 ---> 0f86e6f273aa
Successfully built 0f86e6f273aa
Successfully tagged himanshu/visits:latest
```

After building the image let's run the container with imageo

```sh
visits ➤ docker run himanshu/visits

> @ start /app
> node index.js

/app/index.js:5
cost app = express();
     ^^^

SyntaxError: Unexpected identifier
    at wrapSafe (internal/modules/cjs/loader.js:1167:16)
    at Module._compile (internal/modules/cjs/loader.js:1215:27)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1272:10)
    at Module.load (internal/modules/cjs/loader.js:1100:32)
    at Function.Module._load (internal/modules/cjs/loader.js:962:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12)
    at internal/main/run_main_module.js:17:47
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! @ start: `node index.js`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the @ start script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /root/.npm/_logs/2020-08-24T09_08_40_991Z-debug.log
```

We can observe here that that it's giving redis connection error which we will fix further

Now let's run the instance of redis also

```sh
docker run redis
```

Even after running redis docker container we are getting errors because for connecting both container we need some type of networking which can be achieved by either docker-cli network command which include multiple commands to run or we can use of docker compose

![Docker Compose](./images/docker-compose.PNG)

docker-compose CLI will run multiple docker CLI command in single go without writing those command multiple times manually
![Docker Compose 2](./images/docker-compose-2.PNG)

![Node Redis Docker compose](./images/node-redish-docker-compose.PNG)

### Creating a docker compose file


```yml
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    build: .
    ports:
      - "4001:8081"
```

If we observe above we haven't created any specific connection between the redis and node server. The docker-compose cli will behind the scene will create the network where the redis and node server will able to communicate with each other without any issue

![Auto Connect Multiple Container](./images/auto-connect-between-container.PNG)

Now let's update our `index.js` file to connect our redis server


```js
const express = require('express');

const redis = require('redis');

const app = express();
const client = redis.createClient({
  host: 'redis-server', // As we are using docker-compose we can just give the name of the container name else we need to specify the complete host URL
  port: 6379
});
client.set('visits', 0);

app.get('/', (req, res) => {
  client.get('visits', (err, visits) => {
    res.send('Number of visits is ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('listening on port 8081')
});
```

![Docker compose command](./images/docker-compose-command.PNG)

Now let's run the solution

```sh
visits ➤ docker-compose up --build
Building node-app
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 6b2df94000c7
Step 4/6 : RUN npm install
 ---> Using cache
 ---> bb80d98f6101
Step 5/6 : COPY . .
 ---> Using cache
 ---> f50f61adc528
Step 6/6 : CMD ["npm", "start"]
 ---> Using cache
 ---> bd1a8327f576
Successfully built bd1a8327f576
Successfully tagged visits_node-app:latest
Starting visits_redis-server_1 ... done
Starting visits_node-app_1     ... done
Attaching to visits_redis-server_1, visits_node-app_1
redis-server_1  | 1:C 24 Aug 2020 10:05:35.092 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis-server_1  | 1:C 24 Aug 2020 10:05:35.092 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=1, just started
redis-server_1  | 1:C 24 Aug 2020 10:05:35.092 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis-server_1  | 1:M 24 Aug 2020 10:05:35.094 * Running mode=standalone, port=6379.
redis-server_1  | 1:M 24 Aug 2020 10:05:35.094 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis-server_1  | 1:M 24 Aug 2020 10:05:35.094 # Server initialized
redis-server_1  | 1:M 24 Aug 2020 10:05:35.094 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis-server_1  | 1:M 24 Aug 2020 10:05:35.095 * Loading RDB produced by version 6.0.6
redis-server_1  | 1:M 24 Aug 2020 10:05:35.095 * RDB age 20 seconds
redis-server_1  | 1:M 24 Aug 2020 10:05:35.095 * RDB memory usage when created 0.79 Mb
redis-server_1  | 1:M 24 Aug 2020 10:05:35.095 * DB loaded from disk: 0.000 seconds
redis-server_1  | 1:M 24 Aug 2020 10:05:35.095 * Ready to accept connections
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
```




### Starting and Stopping docker-compose command to shut down multiple container

![Start and Stop Docker Composed Containers](./images/start-stop-docker-compose.PNG)


```sh
visits ➤ docker-compose down
Stopping visits_node-app_1     ... done
Stopping visits_redis-server_1 ... done
Removing visits_node-app_1     ... done
Removing visits_redis-server_1 ... done
Removing network visits_default
```

### How to handle which crashes

Let's start by making error in `index.js` file so that on start it crashes


```js
const express = require('express');
const redis = require('redis');
const process = require('process');

const app = express();
const client = redis.createClient({
  host: 'redis-server', // As we are using docker-compose we can just give the name of the container name else we need to specify the complete host URL
  port: 6379
});
client.set('visits', 0);

app.get('/', (req, res) => {
  process.exit(0); // The code which gonna crash on call
  client.get('visits', (err, visits) => {
    res.send('Number of visits is ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('listening on port 8081')
});
```

Let's run the solution


```sh
visits ➤ docker-compose up --build
Creating network "visits_default" with the default driver
Building node-app
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 6b2df94000c7
Step 4/6 : RUN npm install
 ---> Using cache
 ---> bb80d98f6101
Step 5/6 : COPY . .
 ---> 91bc0d98097c
Step 6/6 : CMD ["npm", "start"]
 ---> Running in 54a0f6fde675
Removing intermediate container 54a0f6fde675
 ---> 62a94a90db4e
Successfully built 62a94a90db4e
Successfully tagged visits_node-app:latest
Creating visits_redis-server_1 ... done
Creating visits_node-app_1     ... done
Attaching to visits_redis-server_1, visits_node-app_1
redis-server_1  | 1:C 24 Aug 2020 10:14:23.810 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis-server_1  | 1:C 24 Aug 2020 10:14:23.810 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=1, just started
redis-server_1  | 1:C 24 Aug 2020 10:14:23.810 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis-server_1  | 1:M 24 Aug 2020 10:14:23.812 * Running mode=standalone, port=6379.
redis-server_1  | 1:M 24 Aug 2020 10:14:23.812 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis-server_1  | 1:M 24 Aug 2020 10:14:23.812 # Server initialized
redis-server_1  | 1:M 24 Aug 2020 10:14:23.813 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis-server_1  | 1:M 24 Aug 2020 10:14:23.814 * Ready to accept connections
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
visits_node-app_1 exited with code 0
```

The output will be as follow

![Crash Output](./images/crash-error.PNG)

The output or `docker ps` command will be as below

```sh
visits ➤ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
03e59778813b        redis               "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes        6379/tcp            visits_redis-server_1
```

As we can see the below error in the console
```sh
visits_node-app_1 exited with code 0
```

### Re-start container after it goes down



![Exit Status](./images/exit-status.PNG)


![Restart Policy](./images/restart-policy.PNG)

Let's start by adding _always start_ policy


```yml
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    restart: always # always restart when container goes down
    build: .
    ports:
      - "4001:8081"
```

Now let's again run the solution with `docker-compose up --build` and open browser and navigate to URL `localhost:4001`


```sh
visits ➤ docker-compose up --build
Building node-app
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 6b2df94000c7
Step 4/6 : RUN npm install
 ---> Using cache
 ---> bb80d98f6101
Step 5/6 : COPY . .
 ---> f6103a739998
Step 6/6 : CMD ["npm", "start"]
 ---> Running in ccacbaa9f958
Removing intermediate container ccacbaa9f958
 ---> b3f1b25d9d60
Successfully built b3f1b25d9d60
Successfully tagged visits_node-app:latest
Recreating visits_node-app_1   ... done
Starting visits_redis-server_1 ... done
Attaching to visits_redis-server_1, visits_node-app_1
redis-server_1  | 1:C 24 Aug 2020 10:32:55.035 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis-server_1  | 1:C 24 Aug 2020 10:32:55.035 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=1, just started
redis-server_1  | 1:C 24 Aug 2020 10:32:55.035 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis-server_1  | 1:M 24 Aug 2020 10:32:55.037 * Running mode=standalone, port=6379.
redis-server_1  | 1:M 24 Aug 2020 10:32:55.037 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis-server_1  | 1:M 24 Aug 2020 10:32:55.038 # Server initialized
redis-server_1  | 1:M 24 Aug 2020 10:32:55.038 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis-server_1  | 1:M 24 Aug 2020 10:32:55.039 * Loading RDB produced by version 6.0.6
redis-server_1  | 1:M 24 Aug 2020 10:32:55.039 * RDB age 9 seconds
redis-server_1  | 1:M 24 Aug 2020 10:32:55.040 * RDB memory usage when created 0.77 Mb
redis-server_1  | 1:M 24 Aug 2020 10:32:55.040 * DB loaded from disk: 0.001 seconds
redis-server_1  | 1:M 24 Aug 2020 10:32:55.041 * Ready to accept connections
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
visits_node-app_1 exited with code 0
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
visits_node-app_1 exited with code 0
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
```

In the end we can observe that whenever it is exiting with `exited with code 0` server getting restarted 


Let's change the policy to `on-failure`


```yml
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    restart: on-failure # restart when container fails
    build: .
    ports:
      - "4001:8081"

```

And at the same time we have to update our exit code in `index.js` file too


```js
const express = require('express');
const redis = require('redis');
const process = require('process');

const app = express();
const client = redis.createClient({
  host: 'redis-server', // As we are using docker-compose we can just give the name of the container name else we need to specify the complete host URL
  port: 6379
});
client.set('visits', 0);

app.get('/', (req, res) => {
  process.exit(1); // The code which gonna give error when called
  client.get('visits', (err, visits) => {
    res.send('Number of visits is ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('listening on port 8081')
});
```


Now let's run again with `docker-compose up --build` command


```sh
visits ➤ docker-compose up --build
Building node-app
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 6b2df94000c7
Step 4/6 : RUN npm install
 ---> Using cache
 ---> bb80d98f6101
Step 5/6 : COPY . .
 ---> 0061655a74cb
Step 6/6 : CMD ["npm", "start"]
 ---> Running in 9906374e0556
Removing intermediate container 9906374e0556
 ---> 6c8d7b5b6749
Successfully built 6c8d7b5b6749
Successfully tagged visits_node-app:latest
Recreating visits_node-app_1   ... done
Starting visits_redis-server_1 ... done
Attaching to visits_redis-server_1, visits_node-app_1
redis-server_1  | 1:C 24 Aug 2020 10:40:39.844 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis-server_1  | 1:C 24 Aug 2020 10:40:39.844 # Redis version=6.0.6, bits=64, commit=00000000, modified=0, pid=1, just started
redis-server_1  | 1:C 24 Aug 2020 10:40:39.844 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis-server_1  | 1:M 24 Aug 2020 10:40:39.846 * Running mode=standalone, port=6379.
redis-server_1  | 1:M 24 Aug 2020 10:40:39.846 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis-server_1  | 1:M 24 Aug 2020 10:40:39.846 # Server initialized
redis-server_1  | 1:M 24 Aug 2020 10:40:39.846 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis-server_1  | 1:M 24 Aug 2020 10:40:39.847 * Loading RDB produced by version 6.0.6
redis-server_1  | 1:M 24 Aug 2020 10:40:39.847 * RDB age 5 seconds
redis-server_1  | 1:M 24 Aug 2020 10:40:39.847 * RDB memory usage when created 0.77 Mb
redis-server_1  | 1:M 24 Aug 2020 10:40:39.847 * DB loaded from disk: 0.000 seconds
redis-server_1  | 1:M 24 Aug 2020 10:40:39.847 * Ready to accept connections
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
node-app_1      | npm ERR! code ELIFECYCLE
node-app_1      | npm ERR! errno 1
node-app_1      | npm ERR! @ start: `node index.js`
node-app_1      | npm ERR! Exit status 1
node-app_1      | npm ERR!
node-app_1      | npm ERR! Failed at the @ start script.
node-app_1      | npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
node-app_1      |
node-app_1      | npm ERR! A complete log of this run can be found in:
node-app_1      | npm ERR!     /root/.npm/_logs/2020-08-24T10_40_43_954Z-debug.log
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
node-app_1      | npm ERR! code ELIFECYCLE
node-app_1      | npm ERR! errno 1
node-app_1      | npm ERR! @ start: `node index.js`
node-app_1      | npm ERR! Exit status 1
node-app_1      | npm ERR!
node-app_1      | npm ERR! Failed at the @ start script.
node-app_1      | npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
node-app_1      |
node-app_1      | npm ERR! A complete log of this run can be found in:
node-app_1      | npm ERR!     /root/.npm/_logs/2020-08-24T10_40_49_029Z-debug.log
visits_node-app_1 exited with code 1
node-app_1      |
node-app_1      | > @ start /app
node-app_1      | > node index.js
node-app_1      |
node-app_1      | listening on port 8081
```

We can observe whenever error occured container re-started 



# Use docker to author and publish to prduction and deploy to cloud (AWS, digital ocean)



![Workflow](./images/workflow.PNG)

The complete workflow will look like as below

![Complete Workflow](./images/complete-workflow.PNG)

## Creating Development workflow using docker

#### We will create a react application wrap it around a docker and deploy it to prduction

Create a react app using scafolding
```sh
npx create-react-app frontend
```

Once the scaffolding is completed we can navigate to the directory of react app and run below commands to create the build


```sh
npm run build
```

To run the solution

```sh
npm run start
```

To run the test cases we can run

```sh
npm run test
```

Now let's create a docker file with the name `Docker.dev` and add required script there

```dockerfile
FROM node:alpine

WORKDIR '/app'

COPY package.json .

RUN npm install

COPY . .

CMD ["npm", "run", "start"]
```

### Buiilding docker image with custom file name as we can see the name is not standard as only `Dockerfile`

```sh
frontend ➤ docker build -f Dockerfile.dev .                     git:master*
Sending build context to Docker daemon  186.4MB
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> c187cb2e7cab
Step 4/6 : RUN pm install
 ---> Running in 90dec9052c04
/bin/sh: pm: not found
The command '/bin/sh -c pm install' returned a non-zero code: 127
frontend ➤ docker build -f Dockerfile.dev .                     git:master*
Sending build context to Docker daemon  186.4MB
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> c187cb2e7cab
Step 4/6 : RUN npm install
 ---> Running in 4b7e69ddebd9
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated @types/testing-library__dom@7.5.0: This is a stub types definition. testing-library__dom provides its own type definitions, so you do not need this installed.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated request-promise-native@1.0.9: request-promise-native has been deprecated because it extends the now deprecated request package, see https://github.com/request/request/issues/3142
npm WARN deprecated @hapi/joi@15.1.1: joi is leaving the @hapi organization and moving back to 'joi' (https://github.com/sideway/joi/issues/2411)
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated @hapi/bourne@1.3.2: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/hoek@8.5.1: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/topo@3.1.6: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/address@2.1.4: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated core-js@2.6.11: core-js@<3 is no longer maintained and not recommended for usage due to the number of issues. Please, upgrade your dependencies to the actual version of core-js@3.
npm WARN deprecated left-pad@1.3.0: use String.prototype.padStart()

> core-js@2.6.11 postinstall /app/node_modules/babel-runtime/node_modules/core-js
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> core-js@3.6.5 postinstall /app/node_modules/core-js
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> core-js-pure@3.6.5 postinstall /app/node_modules/core-js-pure
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)

npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.1.2 (node_modules/react-scripts/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules/jest-haste-map/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN notsup Unsupported engine for watchpack-chokidar2@2.0.0: wanted: {"node":"<8.10.0"} (current: {"node":"14.8.0","npm":"6.14.7"})
npm WARN notsup Not compatible with your version of node/npm: watchpack-chokidar2@2.0.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules/watchpack-chokidar2/node_modules/chokidar/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules/webpack-dev-server/node_modules/chokidar/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN tsutils@3.17.1 requires a peer of typescript@>=2.8.0 || >= 3.2.0-dev || >= 3.3.0-dev || >= 3.4.0-dev || >= 3.5.0-dev || >= 3.6.0-dev || >= 3.6.0-beta || >= 3.7.0-dev || >= 3.7.0-beta but none is installed. You must install peer dependencies yourself.

added 1607 packages from 783 contributors and audited 1611 packages in 68.157s

66 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

Removing intermediate container 4b7e69ddebd9
 ---> ed02ab8d6b07
Step 5/6 : COPY . .
 ---> c54e4d733b64
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Running in a6e421feefb7
Removing intermediate container a6e421feefb7
 ---> 7de30d6a9778
Successfully built 7de30d6a9778
```

We can see it took a lot of time becuase size of folder was high and we are doing npm install for node_modules and at the same time we are copying the node modules to docker images. So let's remove the node_modules from our build and try to run the same command


```sh
frontend ➤ docker build -f Dockerfile.dev .                                    git:master*
Sending build context to Docker daemon  1.335MB
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> e947db310b6e
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> c187cb2e7cab
Step 4/6 : RUN npm install
 ---> Using cache
 ---> ed02ab8d6b07
Step 5/6 : COPY . .
 ---> 40f2998d5e44
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Running in 9a82575c0ac8
Removing intermediate container 9a82575c0ac8
 ---> 2398ef494dda
Successfully built 2398ef494dda
```

It ran very quick as we have very less size of folder

If we want to create image which will run all the changes you do in your local we need to use some more extension and the command will be like below

```sh
docker run -it  --rm -v ${PWD}:/app -v /app/node_modules -p 3001:3000 5b641d87197a
```

Here `-v` stands for `volume` which the container is accessing from local machine

So the docker container use `Volume` here to execute the code

> The drawback of above method is to very long command

So to overcome this we can use `docker-compose` and the purpose of the `docker-compose` to overcome complexity of running all the step in docker file

Let's create our `docker-compose.yml` file


```yml
version: '3.7'

services:
  web:
    container_name: react-app
    stdin_open: true // Do not forget to add this as when development server will start container will auto close
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3001:3000"

    volumes:
      - /app/node_modules
      - .:/app
```

Running test cases in the docker container with adding second service in our `docker-comopse.yml`

```yml
version: '3.7'

services:
  web:
    container_name: react-app
    stdin_open: true
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3001:3000"

    volumes:
      - /app/node_modules
      - .:/app

  tests: // Adding a second service which will create a different container which will be only responsible to run the test cases
    container_name: react-app-test
    build:
      context: .
      dockerfile: Dockerfile.dev

    volumes:
      - /app/node_modules
      - .:/app

    command: ["npm", "run", "test"]
```

Now let's build the `docker-compose.yml` file

```sh
docker-compose up --build                                               git:master*
Building web
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 626fa8baa8e4
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 857bde7cd687
Step 4/6 : RUN npm install
 ---> Using cache
 ---> b1c9573a93a9
Step 5/6 : COPY . .
 ---> 18fbdd306190
Step 6/6 : CMD ["npm", "start"]
 ---> Running in d7a1ff784bd1
Removing intermediate container d7a1ff784bd1
 ---> 55b5d522c1c3

Successfully built 55b5d522c1c3
Successfully tagged frontend_web:latest
Building tests
Step 1/6 : FROM node:alpine
 ---> 0f2c18cef5d3
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 626fa8baa8e4
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 857bde7cd687
Step 4/6 : RUN npm install
 ---> Using cache
 ---> b1c9573a93a9
Step 5/6 : COPY . .
 ---> Using cache
 ---> 18fbdd306190
Step 6/6 : CMD ["npm", "start"]
 ---> Using cache
 ---> 55b5d522c1c3

Successfully built 55b5d522c1c3
Successfully tagged frontend_tests:latest
Recreating react-app-test ... done
Recreating react-app      ... done
Attaching to react-app-test, react-app
react-app-test |
react-app-test | > frontend@0.1.0 test /app
react-app-test | > react-scripts test
react-app-test |
react-app |
react-app | > frontend@0.1.0 start /app
react-app | > react-scripts start
react-app |
react-app | ℹ ｢wds｣: Project is running at http://172.21.0.3/
react-app | ℹ ｢wds｣: webpack output is served from
react-app | ℹ ｢wds｣: Content not from webpack is served from /app/public
react-app | ℹ ｢wds｣: 404s will fallback to /
react-app | Starting the development server...
react-app |
react-app-test | PASS src/App.test.js
react-app-test |   ✓ renders learn react link (88ms)
react-app-test |   ✓ renders learn react link 2 (9ms)
react-app-test |   ✓ renders learn react link 3 (6ms)
react-app-test |
react-app-test | Test Suites: 1 passed, 1 total
react-app-test | Tests:       3 passed, 3 total
react-app-test | Snapshots:   0 total
react-app-test | Time:        4.456s
react-app-test | Ran all test suites.
react-app-test |
react-app | Compiled successfully!
react-app |
react-app | You can now view frontend in the browser.
react-app |
react-app |   Local:            http://localhost:3000
react-app |   On Your Network:  http://172.21.0.3:3000
react-app |
react-app | Note that the development build is not optimized.
react-app | To create a production build, use yarn build.
react-app |
```

We can observe above there are two container's running one with `react-app-tes` and another with `react-app`

Now let's attach to test container and try to run the test commands

```sh
docker attache d8f8b2ca5229
```

When we attach to docker container it attach to main starter program like npm in the 

Let's create our dockerfile for prduction

We will have multistage build process for production


![Multistage Build Process](./images/multi-stage-build.PNG)

Now let's create our production docker file

```dockerfilesh
FROM node:alpine as builder

WORKDIR '/app'

COPY package.json ./

RUN npm install

COPY . .

RUN npm run build

FROM nginx

COPY --from=builder /app/build /usr/share/nginx/html
```

Now lets build the image and run the container
```sh
frontend ➤ Docker build .                                                          git:master*
Sending build context to Docker daemon  842.8kB
Step 1/8 : FROM node:alpine as builder
 ---> 0f2c18cef5d3
Step 2/8 : WORKDIR '/app'
 ---> Using cache
 ---> 626fa8baa8e4
Step 3/8 : COPY package.json ./
 ---> 954594e7d381
Step 4/8 : RUN npm install
 ---> Running in 41f79fd53c5a
npm WARN deprecated @types/testing-library__dom@7.5.0: This is a stub types definition. testing-library__dom provides its own type definitions, so you do not need this installed.
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated request-promise-native@1.0.9: request-promise-native has been deprecated because it extends the now deprecated request package, see https://github.com/request/request/issues/3142
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated @hapi/joi@15.1.1: joi is leaving the @hapi organization and moving back to 'joi' (https://github.com/sideway/joi/issues/2411)
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated @hapi/bourne@1.3.2: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/hoek@8.5.1: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/topo@3.1.6: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated @hapi/address@2.1.4: This version has been deprecated and is no longer supported or maintained
npm WARN deprecated left-pad@1.3.0: use String.prototype.padStart()
npm WARN deprecated core-js@2.6.11: core-js@<3 is no longer maintained and not recommended for usage due to the number of issues. Please, upgrade your dependencies to the actual version of core-js@3.

> core-js@2.6.11 postinstall /app/node_modules/babel-runtime/node_modules/core-js
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> core-js@3.6.5 postinstall /app/node_modules/core-js
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)


> core-js-pure@3.6.5 postinstall /app/node_modules/core-js-pure
> node -e "try{require('./postinstall')}catch(e){}"

Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
> https://opencollective.com/core-js
> https://www.patreon.com/zloirock

Also, the author of core-js ( https://github.com/zloirock ) is looking for a good job -)

npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.1.2 (node_modules/react-scripts/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules/jest-haste-map/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN notsup Unsupported engine for watchpack-chokidar2@2.0.0: wanted: {"node":"<8.10.0"} (current: {"node":"14.8.0","npm":"6.14.7"})
npm WARN notsup Not compatible with your version of node/npm: watchpack-chokidar2@2.0.0
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules/watchpack-chokidar2/node_modules/chokidar/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.2.7 (node_modules/webpack-dev-server/node_modules/chokidar/node_modules/fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.13: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
npm WARN tsutils@3.17.1 requires a peer of typescript@>=2.8.0 || >= 3.2.0-dev || >= 3.3.0-dev || >= 3.4.0-dev || >= 3.5.0-dev || >= 3.6.0-dev || >= 3.6.0-beta || >= 3.7.0-dev || >= 3.7.0-beta but none is installed. You must install peer dependencies yourself.

added 1607 packages from 783 contributors and audited 1611 packages in 60.987s

66 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

Removing intermediate container 41f79fd53c5a
 ---> 3dec9f2b7861
Step 5/8 : COPY . .
 ---> 24662d533ec9
Step 6/8 : RUN npm run build
 ---> Running in 73a1bda43ed9

> frontend@0.1.0 build /app
> react-scripts build

Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  39.38 KB  build/static/js/2.c24a221a.chunk.js
  770 B     build/static/js/runtime-main.c8a21426.js
  708 B     build/static/js/main.0e8d59eb.chunk.js
  547 B     build/static/css/main.5f361e03.chunk.css

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  yarn global add serve
  serve -s build

Find out more about deployment here:

  bit.ly/CRA-deploy

Removing intermediate container 73a1bda43ed9
 ---> 90e168f862f9
Step 7/8 : FROM nginx
latest: Pulling from library/nginx
bf5952930446: Pull complete
cb9a6de05e5a: Pull complete
9513ea0afb93: Pull complete
b49ea07d2e93: Pull complete
a5e4a503d449: Pull complete
Digest: sha256:b0ad43f7ee5edbc0effbc14645ae7055e21bc1973aee5150745632a24a752661
Status: Downloaded newer image for nginx:latest
 ---> 4bb46517cac3
Step 8/8 : COPY --from=builder /app/build /usr/share/nginx/html
 ---> a1e5a04a61c1
Successfully built a1e5a04a61c1
```

Now lets run the container by port forwarding
```sh
frontend ➤ docker run -p 8080:80 a1e5a04a61c1                                      git:master*
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET / HTTP/1.1" 200 2217 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /static/css/main.5f361e03.chunk.css HTTP/1.1" 200 943 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /static/js/2.c24a221a.chunk.js HTTP/1.1" 200 129440 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /static/js/main.0e8d59eb.chunk.js HTTP/1.1" 200 1362 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /static/media/logo.5d5d9eef.svg HTTP/1.1" 200 2671 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /favicon.ico HTTP/1.1" 200 3150 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /manifest.json HTTP/1.1" 200 492 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
172.17.0.1 - - [29/Aug/2020:06:56:06 +0000] "GET /logo192.png HTTP/1.1" 200 5347 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.135 Safari/537.36" "-"
```

Open brower and navigate to `http://localhost:8080/` you will able to see the home page of `create-react-app`

## Now let's work on CICD pipeline


![CICD](./images/cicd-flow.PNG)

## Setting up Travis CI
