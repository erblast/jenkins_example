# Jenkins the Basics

[tutorial](https://www.jenkins.io/doc/tutorials/build-a-python-app-with-pyinstaller/)

In a nutshell:

- jenkins pipelines can run docker commands howver for this they need to spawn a sibling container that provides docker called docker in docker `docker:dind`. Instead of using `dind` you can also expose a docker socket as describved [here](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/).
- for this we need to create a docker network and a dedicated docker volume
- jenkins itself can run in its own container `jenkinsci/blueocean` and be accessed via [localhost:8080](localhost:8080)
- additionally a docker volume is created that preserves configuration changes made via the jenkins GUI

# Create Volumes and Network
```
docker network create jenkins
docker volume create jenkins-docker-certs
docker volume create jenkins-data
```

# Startup `dind`
```
docker container run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --volume "$HOME":/home docker:dind
```

# Startup `blueocean`
```
docker container run --name jenkins-tutorial --rm --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  --volume "$HOME":/home --publish 8080:8080 jenkinsci/blueocean
```

# First Run
- unlock jenkins
- create admin user
- see [tutorial unlcok section](https://www.jenkins.io/doc/tutorials/build-a-python-app-with-pyinstaller/#setup-wizard)


# Setting up CI/CD for github repository
- create new multibranch pipeline and follow instructions
- add Jenkinsfile to project repo
- consider adding github API token to stored credentials


