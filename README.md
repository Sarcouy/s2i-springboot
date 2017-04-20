SpringBoot - CentOS Docker image
========================================

All of that is sampled from [Wildfly openshift s2i project](https://github.com/openshift-s2i/s2i-wildfly)

This repository contains the source for building various versions of
the Tomcat application as a reproducible Docker image using
[source-to-image](https://github.com/openshift/source-to-image).
The resulting image can be run using [Docker](http://docker.io).

Versions
---------------
CentOS versions currently provided are:
* CentOS7

Java versions currently provided are:
* Openjdk-6
* Openjdk-7
* Openjdk-8

Maven versions currently provided are:
* maven-3.2.5
* maven-3.3.9
* maven-3.5.0

Installation
---------------

This image is available on DockerHub.  To download it, run:

```
$ docker pull sarcouy/s2i-springboot:jdk$JDK_VERSION-mvn$MAVEN_VERSION
```

for example

```
$ docker pull sarcouy/s2i-springboot:jdk8-mvn3.3.9 
```


Usage
---------------------
To build a simple `java application`
using standalone [S2I](https://github.com/openshift/source-to-image) and then run the
resulting image with [Docker](http://docker.io) execute:

```
$ s2i build -e JAR_NAME=app.jar -e INCREMENTAL=false git://github.com/example/openshift-springboot-example sarcouy/s2i-springboot:jdk8-mvn3.3.9 springboottest
$ docker run -p 8080:8080 springboottest
```

If you want to use an incremental build, don't forget -e `INCREMENTAL=true`

```
$ s2i build -e JAR_NAME=app.jar -e INCREMENTAL=true --incremental git://github.com/example/openshift-springboot-example sarcouy/s2i-springboot:jdk8-mvn3.3.9 springboottest
$ docker run -p 8080:8080 springboottest
```

**Accessing the application:**
```
$ curl 127.0.0.1:8080
```


Repository organization
------------------------
* **`springboot`**
    * **`<Maven-version>`**
        * **`<Java-version>`**

            * **Dockerfile**

                CentOS based Dockerfile

            * **`s2i/bin/`**

                This folder contains scripts that are run by [S2I](https://github.com/openshift/source-to-image):

                *   **assemble**

                  Is used to restore the build artifacts from the previous build (in case of
                  'incremental build'), to install the sources into location from where the
                  application will be run and prepare the application for deployment (eg.
                  installing maven dependencies, building java code, etc..).


                *   **run**

                  This script is responsible for running the application.

                *   **save-artifacts**

                  In order to do an *incremental build* (iow. re-use the build artifacts
                  from an already built image in a new image), this script is responsible for
                  archiving those. In this image, this script will archive the
                  maven dependencies.

            * **`contrib/`**

                * **setting.xml**
                    A random setting.xml file


Image version structure
------------------------
##### Structure: openshift/1-2-3

1. Java version - jdk7
2. a dash "-"
3. Maven version - mvn3.2.5

Example: `sarcouy/s2i-springboot:jdk8-mvn3.2.5`
Environment variables
---------------------
To set environment variables, you can place them as a key value pair into a `.sti/environment` 
file inside your source code repository or add -e FOO=BAR to `s2i build -e FOO=BAR` .

* MAVEN_ARGS

    Overrides the default arguments passed to maven durin the build process

* MAVEN_ARGS_APPEND

    This value will be appended to either the default maven arguments, or the value of MAVEN_ARGS if MAVEN_ARGS is set.

* INCREMENTAL

    This is a boolean :
    Set as "true" it avoid removing .m2 in between two builds (but that makes heavier images). Must be used with `s2i build --incremental`
    Default to false

* JAR_NAME

    Name of the war file to run after maven build `JAR_NAME=myApp.jar`

* POM_PATH

    Usefull for many pom.xml git repositories, specify the path to follow into the repo to find the pom file to use. default to `POM_PATH=.`

Copyright
--------------------

Released under the Apache License 2.0. See the [LICENSE](LICENSE) file.

