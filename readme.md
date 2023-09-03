Step 1:first Run Frontend and backend with volumen and network
1. git clone 
2. mongodb://localhost:27017/course-goals
3. docker run  --name mongodb --rm -d -p 27017:27017 mongo
3. docker build -t backend .   
4. docker build -t frontend .
5. docker run --name backend_c --rm backend
6. you will fetch mongodb localhost error
7. which can be solve using mongodb://host.docker.internal:27017/course-goals

   but it is not the solution because if we use host.docker.internal then the frontend is not able to talk backend because backend running on container and not publishing any port

8. docker run --name backend_c --rm -d -p 8000:8000 backend  

9. now run the frontend 
   docker run --name frontend_c --rm -p 3000:3000 -it frontend 
   Now the frontend is connected with backend and api accessing using localhost:8000 post but backend and mongodb running with container

STEP 2: 
10. create the network to communicate between container 
    $ docket create network goal-net

11. And then run the mongodb container with network
    $docker run --name mongodb -d --network goal-net mongo 
    And now if we are using container we should change hoat.docker.internal to mongodb with the mongo container name 
    Same are in the frontend we need to changes the api routes which is till then using localhost:8000 now it will run 
    http://backend_c/goals
12. Now rebuild the images of backend and frontend and try to run the container
    For frontend cmd:
    $ docker run --name frontend_c --network goal-net --rm -p 3000:3000 -it frontend 

14. But there is one error in frontend 
    http://backend_c/goals
    And --network goal-net not work and we don't need because frontend work with Browser not on server so we will remove both and use 
    So finally we use 
    http://localhost:8000/goals
    FOr frontend 
    $ docker run --name frontend_c --rm -p 3000:3000 -it frontend 
    And foR backend
    $ docker  run --name backend_c --rm -d -p 8000:8000 --network goal-net backend

STEP 3:
15. Now we add data persistent 
    Now add volume for data servive if the container will stop or remove
    Add Named volume in mongodb
    Stop mongodb container and run the cmd with volume 
    $ docker run --name mongodb  -v data:/data/db --rm -d --network goal-net mongo
    For security we can use root and pass when running mongodb container 
    $ docker run --name mongodb  -v data:/data/db --rm -d --network goal-net -e MONGO_INITDB_ROOT_USERNAME=max MONGO_INITDB_ROOT_PASSWORD=secret mongo

    Alos all mongo db url in backend with usename password
    mongodb://max:secret@mongodb:27017/courae-goals?authSource=admin
    Now stop the mongodb container and 
    Build the backend image and run container both node and mongodb
    Add Named volume  in backend container
    docker  run --name backend_c -v pathofproject folder:/app -v logs:/app/logs -v /app/node_modules --rm -d -p 8000:8000 --network goal-net backend
    Example :
    docker run --name backend_c --network goals-net --rm -d -p 8000:8000 -v logs:/app/logs -v /app/node_modules -v"/Users/macbookair/Downloads/multi-01-starting-setup/backend:/app" backend 


16. We can also use username and password in env 
    Add env in docker file 
    ENV MONGODB_USERNAME=max
    ENV MONGODB_PASSWORD=secret
    And in backend source code replace
    mongodb://${process.env.MONGODB_USERNAME}:${process.env.MONGODB_PASSWORD}@mongodb:27017/courae-goals?authSource=admin

STEP 4:
17. Also user dockerignore file
    .dockerignore
    /node_modules
    Dockerfile
    .git

STEP 5:
18. Also add bind mount in 
    $ docker run --name frontend_c -v pathofprojectfolder/src:/app/src --rm -p 3000:3000 -it frontend
    Add dockerignore to ignore node_modules
    /node_modules
    Dockerfile
    .git   

19. to see the logs og backend 
    docker logs backend_c





