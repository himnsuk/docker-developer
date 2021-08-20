## __Kubernetes Learning__

At this point of time i have completed docker, container creation, docker-compose and other functionality realted to docker

I have put 4 images of `complex` project on _[https://hub.docker.com](https://hub.docker.com)_

1. [https://hub.docker.com/repository/docker/himnsuk/multi-worker](https://hub.docker.com/repository/docker/himnsuk/multi-worker)
2. [https://hub.docker.com/repository/docker/himnsuk/multi-server](https://hub.docker.com/repository/docker/himnsuk/multi-server)
3. [https://hub.docker.com/repository/docker/himnsuk/multi-nginx](https://hub.docker.com/repository/docker/himnsuk/multi-nginx)
4. [https://hub.docker.com/repository/docker/himnsuk/multi-client](https://hub.docker.com/repository/docker/himnsuk/multi-client)

### Let's work on first Goal -> Get the mult-client image running on our local Kubernetes Cluster running as container

Below are the steps to achieve the goal


![Goals](./k8s-images/first-goal-steps.PNG)

Some knowledge we can use from docker compose 

![Docker-Compose - K8S](./k8s-images/docker-k8s.PNG)

Below are the steps to achieve the goal

![Steps](./k8s-images/steps-to-work.PNG)

We have to create two files `client-pod.yml` and `client-node-port.yml`

```yml
# client-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: client-pod
  labels:
    componet: web

spec:
  containers:
    - name: client
      image: himnsuk/multi-client
      ports:
        - containerPort: 3000
```


```yml
# client-node-port.yml
apiVersion: v1
kin: Service
metadata:
  name: client-node-port

spec:
  type: NodePort
  port:
    - ports: 3050
      targetPOrt: 3000
      nodePort: 31515
  selector:
    component: web
```

Below are the explanation

![Internal Understanding](./k8s-images/internal-happening.PNG)


![Scope](./k8s-images/scope.PNG)

Pod explanation


![Pod Explanation](./k8s-images/pod-explanation.PNG)


![Pod Vs Services](./k8s-images/pods-services.PNG)

![Services Details](./k8s-images/services-details.PNG)


![Working Architecture](./k8s-images/working-architecture.PNG)


![More Detailed Configuration](./k8s-images/more-details.PNG)


![Ports Explanation](./k8s-images/ports-explain.PNG)


![Uploading To Kubectl](./k8s-images/uploading-to-kubectl.PNG)


![Key notes](./k8s-images/key-notes.PNG)

### Stopping or deleting a existing pods


![Delete Pod](./k8s-images/delete-pod.PNG)


```sh
kubectl delete -f client-pod.yaml
#pod "client-pod" deleted
```

Let's deploy our new `client-deployment.yaml` file

```sh
kubectl apply -f client-deployment.yamlj
```

To check a deployment

```sh
kubectl get deployments
```

Updating image version and get cluster to update

![Update Image](./k8s-images/update-image-version.PNG)


Configure vm to use docker server

```sh
eval $(minikube -p minikube docker-env)
```

![configure vm to use docker server](./k8s-images/config-vm-to-use-docker-server.PNG)



![Remember Command](./k8s-images/remember-command.PNG)


### Why to go inside minikube VM and check container status


![why-mess-with-docker-in-the-node](./k8s-images/why-mess-with-docker-in-the-node.PNG)


![high-level-overview-of-general-architecture](./k8s-images/high-level-overview-of-general-architecture.PNG)



![path-to-production](./k8s-images/path-to-production.PNG)
