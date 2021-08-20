### Connecting to VM machine of docker-toolbox on OSX
```shell
docker-machine ssh default
```


## After connecting to Docker VM using SSH command we can use docker commands which will be communicated using API

### Checking the docker version

```shell
docker version
```

### Testing docker working

```shell
docker run busybox echo hello world
```


### To get any help regarding command

```shell
docker --help
```

### Creating container using barebone ubuntu

```shell
docker run -it ubuntu bash
```

* -it is shorthand for -i -t
  * -i tells Docker to connect us to container stdin
  * -t tells Docker to connect with the psuedo-terminal

Now as we have access to terminal of the ubuntu container lets start doing something
```sh
root@d92713449f19:/# figlet hello world
bash: figlet: command not found // It will give error as it's not installed so let's install

root@d92713449f19:/# apt-get update
root@d92713449f19:/# apt-get install figlet

root@d92713449f19:/# figlet hello world

 _          _ _                            _     _
| |__   ___| | | ___   __      _____  _ __| | __| |
| '_ \ / _ \ | |/ _ \  \ \ /\ / / _ \| '__| |/ _` |
| | | |  __/ | | (_) |  \ V  V / (_) | |  | | (_| |
|_| |_|\___|_|_|\___/    \_/\_/ \___/|_|  |_|\__,_|
```

To check how many packages this ubuntu machine has we can run below command inside the 
```sh
root@d92713449f19:/# dpkg -l | wc -l
### 98
```


### Testing docker working

```shell
docker run busybox echo hello world
```
docker run jpetazzo/clock
docker ps
docker ps
docker container ls
docker images
docker ps
docker ps -l
docker ps -q
history
