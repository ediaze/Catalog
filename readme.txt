git clone https://github.com/ediaze/net-catalog-01.git
git config --local user.name "ediaze@gmail.com"
git config --local user.password "****"

# Create the web api project
dotnet new webapi -n Catalog

git add .
git commit -m "Add document file"
git push origin master

# Add the mongo DB package to the c# project 
dotnet add package MongoDB.Driver

# Add an Mongo DB image and set a volume to keep the data after the image is down/stop
docker run -d --rm --name mongo -p 27017:27017 -v mongodbdata:/data/db mongo

# Check the image was created
docker ps

# Stop mongo container
docker stop mongo

# Find the valume
docker volume ls

# Delete a volume
docker volume rm mongodbdata

# Set environment variables
docker run -d --rm --name mongo -p 27017:27017 -v mongodbdata:/data/db -e MONGO_INITDB_ROOT_USERNAME=mongoadmin -e MONGO_INITDB_ROOT_PASSWORD=Pass#word1 mongo

# .NET Secret Manager
dotnet user-secrets init
dotnet user-secrets set MongoDbSettings:Password Pass#word1

# Health checks
dotnet add package AspNetCore.HealthChecks.MongoDb

# Create the docker image from c# project
docker build -t catalog:v1 .

# Create a network to work with other image at the same time
docker network create net5tutorial

# To get all created networks
docker network ls

# Run a docker container on a network
docker run -d --rm --name mongo -p 27017:27017 -v mongodbdata:/data/db -e MONGO_INITDB_ROOT_USERNAME=mongoadmin -e MONGO_INITDB_ROOT_PASSWORD=Pass#word1 --network=net5tutorial mongo

# To list created images
docker images

# To run docker images [local:container]
# define enviroment variables at the same line
docker run -it --rm -p 8080:80 -e MongoDbSettings:Host=mongo -e MongoDbSettings:Password=Pass#word1 --network=net5tutorial catalog:v1

# Share my image
https://hub.docker.com
docker login
docker tag catalog:v1 ediaze/catalog:v1
docker push ediaze/catalog:v1

# Remove an image
docker rmi ediaze/catalog:v1
docker rmi catalog:v1

# log out from docker hub
docker logout

# download and run the shared image
docker run -it --rm -p 8080:80 -e MongoDbSettings:Host=mongo -e MongoDbSettings:Password=Pass#word1 --network=net5tutorial ediaze/catalog:v1