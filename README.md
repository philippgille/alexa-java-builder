**Alexa Java Builder** Docker Image
===================================

This repository contains the necessary files for the *Alexa Java Builder* Docker image.

Supported tags and respective `Dockerfile` links
------------------------------------------------

- [`alpine`, `latest` (alpine/Dockerfile)](https://github.com/philippgille/docker-alexa-java-builder/blob/master/alpine/Dockerfile)
- [`debian` (Dockerfile)](https://github.com/philippgille/docker-alexa-java-builder/blob/master/Dockerfile)

Usage
-----

```bash
docker run --rm -v /path/to/pom-dir:/usr/src/mymaven philippgille/alexa-java-builder
ls /path/to/pom-dir/target/
example-skill-1.0.jar  example-skill-1.0-jar-with-dependencies.jar  archive-tmp  classes  generated-sources  maven-archiver  maven-status
```

Details
-------

- You could achieve the same end result - the packaged JAR - with the command:
    - `mvn assembly:assembly -DdescriptorId=jar-with-dependencies`
- But what if you don't have Maven installed, or an incompatible version? You could use docker and run:
    - `docker run --rm -v /path/to/pom-dir:/usr/src/mymaven -w /usr/src/mymaven maven:3.3-jdk-8-alpine mvn assembly:assembly -DdescriptorId=jar-with-dependencies`
- But that downloads all the dependencies every time you build the JAR. So you could run:
    - `docker run --rm -v /path/to/pom-dir:/usr/src/mymaven -v "/path/to/maven-repo":/root/.m2 -w /usr/src/mymaven maven:3.3-jdk-8-alpine mvn assembly:assembly -DdescriptorId=jar-with-dependencies`
- But what if you don't want any downloaded dependencies on your local machine? What if you want your build machine / environment to be completely stateless?
    - Well, then you create a Docker image that already contains all the dependencies. Which is exactly what this repository contains. Including default values for working directory and launch command.

### Details about the Dockerfile

- Why is `assembly:assembly` used instead of `dependency:resolve`?
    - When using an image that used `dependency:resolve` to create an image layer containing downloaded dependencies to build an Alexa skill jar with dependencies with Maven later, many more files would have to get downloaded. Just try out yourself.
- Why is `-s /usr/share/maven/ref/settings-docker.xml` used when creating the image layer?
    - By default the Maven Docker image uses `/root/.m2` as repository location. But that's configured as volume, so all data would be lost after build time. See the `README` of the official Maven docker container: [Link](https://github.com/carlossg/docker-maven/blob/master/README.md)
- Why is `-s /usr/share/maven/ref/settings-docker.xml` needed in the `CMD` line?
    - Otherwise `/root/.m2` would be used as repository location. But that's configured as volume, so it would be empty when a container gets started from this image. See the `README` of the official Maven docker container: [Link](https://github.com/carlossg/docker-maven/blob/master/README.md)