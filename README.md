# NET 5 REST API

Net 5 project to learn more about API development. Entity, Repository and Controller

## Source

```
Julio Casal
```
* [.NET 5 REST API Tutorial: 01 Getting Started](https://youtu.be/bgk8N_rx1F4).
* [.NET 5 REST API Tutorial: 02 Entity, Repository, Controller (GET)](https://youtu.be/ba08pvLjZFc).
* [.NET 5 REST API Tutorial: 03 Dependency Injection, DTOs](https://youtu.be/n7jmIG-0ORc).
* [.NET 5 REST API Tutorial: 04 POST, PUT, DELETE](https://youtu.be/g3QLOZ4SJHw).
* [.NET 5 REST API Tutorial: 05 Persisting entities with MongoDB](https://youtu.be/E3F-L-CcACQ).
* [.NET 5 REST API Tutorial: 06 Tasks, Async and Await](https://youtu.be/ZFPMNSPzuTY).
* [.NET 5 REST API Tutorial: 07 Secrets and Health Checks](https://youtu.be/dGR6O0j7AmE)
* [.NET 5 REST API Tutorial: 08 Docker](https://youtu.be/wQSuZFd01tY)
* [.NET 5 REST API Tutorial: 09 Kubernetes](https://youtu.be/OTYlUGUy23Y)
* [.NET 5 REST API Tutorial: 10 Unit Testing and TDD](https://youtu.be/dsD0CMgPjUk)

## Git commands

Clone the repo from github
```bash
git clone https://github.com/myuser/net-catalog-01.git
git config --local user.name "myuser"
git config --local user.password "mypwd"
```

Upload the changes
```bash
git add .
git commit -m "Add document file"
git push origin master
```

## dotnet commands

Create the web api project
```bash
dotnet new webapi -n Catalog
```

Add the mongo DB package to the c# project 
```bash
dotnet add package MongoDB.Driver
```

.NET Secret Manager
```bash
dotnet user-secrets init
dotnet user-secrets set MongoDbSettings:Password Pass#word1
```

Add Health checks
```bash
dotnet add package AspNetCore.HealthChecks.MongoDb
```

## docker commands

Add an Mongo DB image and set a volume to keep the data after the image is down/stop
```bash
docker run -d --rm --name mongo -p 27017:27017 -v mongodbdata:/data/db mongo
```

Check the image was created
```bash
docker ps
```

Stop mongo container
```bash
docker stop mongo
```

Find the valume
```bash
docker volume ls
```

Delete a volume
```bash
docker volume rm mongodbdata
```

Set environment variables
```bash
docker run -d --rm --name mongo -p 27017:27017 -v mongodbdata:/data/db -e MONGO_INITDB_ROOT_USERNAME=mongoadmin -e MONGO_INITDB_ROOT_PASSWORD=Pass#word1 mongo
```

Create the docker image from c# project
```bash
docker build -t catalog:v1 .
```

Create a network to work with other image at the same time
```bash
docker network create net5tutorial
```

To get all created networks
```bash
docker network ls
```

Run a docker container on a network
```bash
docker run -d --rm --name mongo -p 27017:27017 -v mongodbdata:/data/db -e MONGO_INITDB_ROOT_USERNAME=mongoadmin -e MONGO_INITDB_ROOT_PASSWORD=Pass#word1 --network=net5tutorial mongo
```

To list created images
```bash
docker images
```

To run docker images [local:container]
define enviroment variables at the same line
```bash
docker run -it --rm -p 8080:80 -e MongoDbSettings:Host=mongo -e MongoDbSettings:Password=Pass#word1 --network=net5tutorial catalog:v1
```

Share my image
```bash
https://hub.docker.com
docker login
docker tag catalog:v1 myuser/catalog:v1
docker push myuser/catalog:v1
```

Remove an image
```bash
docker rmi myuser/catalog:v1
docker rmi catalog:v1
```

log out from docker hub
```bash
docker logout
```

download and run the shared image
```bash
docker run -it --rm -p 8080:80 -e MongoDbSettings:Host=mongo -e MongoDbSettings:Password=Pass#word1 --network=net5tutorial myuser/catalog:v1
```

## Kubernetes commands

Get kubernetes cluster
```bash
kubectl config current-context
```

View the kube config file
```bash
kubectl config view
kubectl version
kubectl config get-contexts
```

Set de context
```bash
kubectl config set current-context docker-desktop
```

If **docker-desktop** does not exists for kubernates you can create
```bash
docker context create k8s-test \
  --default-stack-orchestrator=kubernetes \
  --kubernetes config-file=/home/ubuntu/.kube/config \
  --docker host=unix:///var/run/docker.sock
  ```

To create a new docket context
```bash
docker context create docker-test \
  --default-stack-orchestrator=swarm \
  --docker host=unix:///var/run/docker.sock
```

To use the context for k8s run
```bash
docker context ls
docker context use k8s-test
```

To create a secret
```bash
kubectl create secret generic catalog-secrets --from-literal=mongodb-password='Pass#word1'
```

To deploy to k8s the application
```bash
kubectl apply -f catalog.yaml
kubectl get deployments
kubectl get pods
```

To see the logs from a pod
```bash
kubectl logs catalog-deployment-5d4b7565fc-rhzzc
```

To deploy to k8s mongodb database
```bash
kubectl apply -f mongodb.yaml
kubectl get statefulsets
kubectl get pods
```