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
- Optimize Docker images
