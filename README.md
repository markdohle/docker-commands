# docker-commands
List of Docker Commands

### Usefull Docker Commands

Build image

```
# The -t gives your image a name
$ docker build -t YourImageName.
```

List Containers

```
# Containers currently running
$ docker ps -a
```

List Images

```
# List all Docker images
$ docker images -a
```

Delete containers

```
# Delete every Docker container
# Run this command first because images are attached to containers
$ docker rm $(docker ps -a -q)
```

Delete images

```
# Delete every Docker image
$ docker rmi $(docker images -q)
```

Force delete images

```
# to force delete images to prevent their use
$ docker rmi -f $(docker images -q)
```

Stop container
```
# Stop a container
$ docker stop <container id>
```

Shell in running container

```
# From Docker 1.3 onwards
$ sudo docker exec -i -t <containerIdOrName> bash
```

### Create and Publish A Docker Image

Login to [docker hub](http://hub.docker.com/) and create a repository for your image. Once you are done creating the image, you will push into this repository.

Create an application and a docker file for the application. Then, create a docker image for the application. Name your image YourDockerUsername/YourImageName. In this example the username is "markdohle" and the image name is "cabinfever".
```
# Create Docker image
$ docker build -t markdohle/cabinfever.
```
Test your new image.
```
# "-p 8080:3000" sets the container port to 3000 and the host port to 8080
# "--name cabinfeverInstance" sets the container name to cabinfeverInstance
# "-t cabinfever" references the cabinfever image
$ docker run -p 8080:3000 --name cabinfeverInstance -t markdohle/cabinfever
```
After you confirm your application works, push your image to docker hub.
```
# Note the username/image matches the Docker repository
$ docker push markdohle/cabinfever
```
To use the image, enter the pull command.
```
$ docker pull markdohle/cabinfever
```

### Docker - NodeJS - Barebones Application

Create a directory named docnode. Initialize your application (npm init). Install express (npm install express --save). Your package.json file should look like the following:
```
{
  "name": "docnode",
  "version": "1.0.0",
  "description": "barebones node on docker",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "abel@mit.edu",
  "license": "MIT",
  "dependencies": {
    "express": "^4.15.5"
  }
}
```

Add a bare-bones express server and name your file index.js:

```
var express = require('express');
var app     = express();
 
app.get('/', function(req,res){
  res.send('Hello World!');
});
 
var port = 3000;
app.listen(port,function(){
  console.log('Listening on port:' + port);
});
```

Create a Docker file and name it Dockerfile:

```
FROM node:slim
MAINTAINER abelsan <abel@mit.edu>
WORKDIR /app
# copy code, install npm dependencies
COPY index.js /app/index.js
COPY package.json /app/package.json
RUN npm install
```

Create a Docker compose file and name it docker-compose.yml:

```
version: "2"
services:
  gin:
    build: .
    ports:
      - "3000:3000"
```

If needed, copy your code to the remote machine. After this is done, at the command line, enter docker-compose up:
```
docker-compose up
```

### Install Docker on Upuntu

Step 1 — Installing Docker

The Docker installation package available in the official Ubuntu 16.04 repository may not be the latest version. To get the latest version, install Docker from the official Docker repository. This section shows you how to do that.

First, add the GPG key for the official Docker repository to the system:
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
Add the Docker repository to APT sources:
```
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
Next, update the package database with the Docker packages from the newly added repository:
```
$ sudo apt-get update
```
Ensure that you install from the Docker repo instead of the default Ubuntu 16.04 repo:
```
apt-cache policy docker-ce
```
You should see output similar to the following:
```
Output of apt-cache policy docker-ce

docker-ce:

Installed: (none)

Candidate: 17.03.1~ce-0~ubuntu-xenial

Version table:

17.03.1~ce-0~ubuntu-xenial 500

500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages

17.03.0~ce-0~ubuntu-xenial 500

500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```
Notice that docker-ce is not installed, but the candidate for installation is from the Docker repository for Ubuntu 16.04. Also note that the docker-ce version number might be different.

Finally, install Docker:
```
$ sudo apt-get install -y docker-ce
```
Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it's running:
```
$ sudo systemctl status docker
```
The output should be similar to the following, showing that the service is active and running:
```
Output

docker.service - Docker Application Container Engine
Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)

Active: active (running) since Sun 2016-05-01 06:53:52 CDT; 1 weeks 3 days ago

Docs: https://docs.docker.com

Main PID: 749 (docker)
```
 

The installation of Docker gives you not just the Docker service (daemon) but also the Docker command line utility or the Docker client. We'll explore how to use the Docker command later in this tutorial.

Step 2 — Executing The Docker Command Without Sudo (Optional)
By default, running the Docker command requires root privileges; therefore, you have to prefix the command with sudo. It can also be run by a user in the Docker group, which is automatically created during the Docker Installation. If you attempt to run the Docker command without prefixing it with sudo or without being in the docker group, you'll get an output like this:
```
Output

docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.

See 'docker run --help'.
```
If you want to avoid typing sudo whenever you run the Docker command, add your username to the Docker group:
```
$ sudo usermod -aG docker ${USER}
```
To apply the new group membership, log out of the server, then log back in.
