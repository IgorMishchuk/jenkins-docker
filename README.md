# Simple example of containerized Nginx and Jenkins with pipeline to rebuild and relaucn Nginx container

## Configuration

### NGINX

NGINX image is [built](../blob/master/nginx/Dockerfile) on `alpine:latest` image and exposes port 90. Image tag is defined [here](../blob/master/docker-compose.yml#L6) and will default to `1.0` if none is explicitly provided.

[Configuration](../blob/master/nginx/nginx.conf) is basic - display [index.html](../blob/master/nginx/web/index.html) on port 90.

### Jenkins

Jenkins image is [built](../blob/master/jenkins/Dockerfile) on `adoptopenjdk/openjdk8:jdk8u262-b10-alpine` image, default [configuration](https://github.com/jenkinsci/docker/blob/master/Dockerfile-alpine) has been updated for current setup:

* [Define](../blob/master/jenkins/Dockerfile#L27-L28) and [add](../blob/master/jenkins/Dockerfile#L41) docker:993 group. Add user `jenkins` to `docker` group [here](../blob/master/jenkins/Dockerfile#L42), so Jenkins can invoke `docker` and `docker-compose` without installing either of those tools on Jenkins master;
* Preconfigure Jenkins:
    * [Create](../blob/master/jenkins/Dockerfile#L54) a user admin/PassW0rd! (**Even if you will start this locally, for sake of discipline, please change the password!**);
    * [Config](../blob/master/jenkins/Dockerfile#L55) of Jenkins instance, as described [here](../blob/master/jenkins/config/jenkins_home_conf.xml):
        * Save user credentials in its own database;
        * Users cannot signup. New users should be defined through Dockerfile;
        * Permission for anonymous is read-only;
    * [Enable](../blob/master/jenkins/Dockerfile#L56) Agent → Master Access Control policy. Policy details are in this [document](https://wiki.jenkins.io/display/JENKINS/Slave+To+Master+Access+Control);
    * [Copy](../blob/master/jenkins/Dockerfile#L57) pre-configured pipeline job to rebuild Nginx image;
    * [Define](../blob/master/jenkins/Dockerfile#L58) which plugins should be pre-installed. Plugin list is [here](../blob/master/jenkins/config/plugins/plugins.txt);
* [Define](../blob/master/jenkins/Dockerfile#L25) and [create](../blob/master/jenkins/Dockerfile#L61) a folder where build config for NGINX image will be stored;
* [Define](../blob/master/jenkins/Dockerfile#L65) newer version (2.235.4 → 2.235.5) and [update](../blob/master/jenkins/Dockerfile#L68) checksum to verify the downloaded `.war` file;
* [Run](../blob/master/jenkins/Dockerfile#L105) installation of predefined plugins.

Jenkins should be available [http://localhost:8080/](http://localhost:8080/)

### Build

To build the images, run `docker-compose build` from repo root folder.

Once build is complete two images will be created:
* `jenkins:1.0`;
* `nginx:1.0`.

### Run

To bring up the application, run `docker-compose up -d` from repo root folder.

Once containers are up you should be able to browse to:
* [http://localhost:80/](http://localhost:80/) for Nginx. Port mapping is defined [here](../blob/master/docker-compose.yml#L14-L15);
* [http://localhost:8080/](http://localhost:8080/) for Jenkins. Login details are [here](../blob/master/README.md#L17)

### Pipeline job

After changes to Nginx files have been done, run job `weissbeerger` in Jenkins. It will automatically tag image version with [build number](../blob/master/Jenkinsfile#L5), rebuild the image and start new container in scope of existing docker application.