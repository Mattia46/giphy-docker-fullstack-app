# Docker Fullstack App 

##### This project would run backend, db and frontend in docker containers
---

#### Create a [giphy token](https://giphy.com/token)

`add you token to the .env file`

---

#### Assumptions

Install [Docker](https://docs.docker.com/desktop/) and
[Docker-compose](https://docs.docker.com/compose/install/)

All the following env name are at your discretinary
```
$NETWORK_NAME => the network name that you'll specify later
$BACKEND_IMAGE_NAME => backend docker image name that you'll create
$FRONTEND_IMAGE_NAME => frontend docker image name that you'll create
$DB_CONTAINER_NAME => db container name
$BACKEND_CONTAINER_NAME => backend container name
$FRONTEND_CONTAINER_NAME => frontend container name
```

### Run it in separate docker containers

##### Create a docker network
```
docker create network $NETWORK_NAME
```

##### Build your docker images
```
// move to the /back folder
docker build -t $BACKEND_IMAGE_NAME .

// move to the /back folder
docker build -t $FRONTEND_IMAGE_NAME .
```

##### Run your containers

###### Run db
```
// $(pwd) is you /db folder

docker run -p 3306:3306 -d --name $DB_CONTAINER_NAME \
-e MYSQL_USER=user -e MYSQL_PASSWORD=mysql -e MYSQL_ROOT_PASSWORD=root \
--rm -v $(pwd)/init.sql:/docker-entrypoint-initdb.d/init.sql \
--network $NETWORK_NAME mysql:5.7
```

###### Run backend 
```
# from root folder because it will get the GIPHY_TOKEN from .env file

docker run -p 3000:3000 -d --name $BACKEND_CONTAINER_NAME --rm --network net --env-file=.env \
-e MYSQL_HOST=$DB_CONTAINER_NAME $BACKEND_IMAGE_NAME 

```

###### Run frontend 
```
# you need to specify the BASE_URL that it has to be the same declared in the $BACKEND_CONTAINER

docker run -d --rm --name $FRONTEND_CONTAINER_NAME -p 8080:8080 \
-e BASE_URL=http://localhost:3000 $FRONTEND_IMAGE_NAME
```

### Run all of them together

##### Assumptions

```
You have already created the above docker images
You have created a docker network
You have named them with same name as in docker-compose.yaml file
```

##### Run them

```
# from the root folder
docker-compose --env-file=.env up -d 

# to stop it
docker-compose down
```
---
#### Manage Docker

##### To list containers
```
docker ps
```

##### To stop single container
```
docker stop $CONTAINER_NAME
```

##### To remove single container
```
docker rm $CONTAINER_NAME
```

##### To delete docker images

```
# list images

docker images 
docker images -a

docker rmi $IMAGE_NAME
```

#### Throubleshooting

##### Check the logs

```
docker logs $CONTAINER_NAME
```


##### run containers in interactive mode to debug them 

```
docker exec -it $CONTAINER_NAME /bin/bash
```
