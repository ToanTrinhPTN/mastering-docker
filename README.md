# mastering-docker

Hand-on use case guideline:

- Build Dockerfile

- Build Docker images

- Manage Docker Registry (tag, version)

  - Create a tag with syntax: 
  
    >   docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

    >   example: docker tag imageName myUsernameInDockerHub/imageName:version1
  
  - Before pushing image, you need to login first. Otherwise you will get an access denied log.
  
  - Login with username and password
  
    > docker login
  
    > enter username and password
  
  - Pushing image to registry with syntax:
  
    > docker push [OPTIONS] NAME[:TAG]
  
    > Example: docker push myUsernameInDockerHub/imageName:version1
    
  
- Manage Docker Network and Volume

```sh
# list out all the current network
$ docker network ls

# create a network named 'my_bridge' with driver bridge
$ docker network create -d bridge my_bridge

# run the container with defaut network bridge 
$ docker run -itd --name=networktest ubuntu

#inspect network bridge, bridge is default and undeleteable
$ docker network inspect bridge 

#disconnect container networktest from network bridge
$ docker network disconnect bridge networktest

# create a volume named 'test-vol' with default driver local
$ docker volume create test-vol

# run with volume test-vol and mount it to /world
$ docker run -d -v test-vol:/world docker-node-ibm
```

- Optimize Docker images
