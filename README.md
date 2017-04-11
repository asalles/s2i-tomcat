Tomcat - CentOS Docker image
========================================

All of that is sampled from [Wildfly openshift s2i project](https://github.com/openshift-s2i/s2i-wildfly)

This repository contains the source for building various versions of
the Tomcat application as a reproducible Docker image using
[source-to-image](https://github.com/openshift/source-to-image).
The resulting image can be run using [Docker](http://docker.io).

Versions
---------------
Tomcat versions currently provided are:
* Tomcat v6
* Tomcat v7
* Tomcat v8
* Tomcat v8.5

CentOS versions currently provided are:
* CentOS7

Java versions currently provided are:
* Openjdk-7
* Openjdk-8

Maven versions currently provided are:
* maven-3.3.3
* maven-3.3.9

Installation
---------------

This image is available on DockerHub.  To download it, run:

```
$ docker pull sarcouy/tomcat:$TOMCAT_VERSION-jdk$JDK_VERSION-mvn$MAVEN_VERSION
```

for example

```
$ docker pull sarcouy/s2itomcat:8.5-jdk8-mvn3.3.9 
```


Usage
---------------------
To build a simple [jee application](https://github.com/bparees/openshift-jee-sample)
using standalone [S2I](https://github.com/openshift/source-to-image) and then run the
resulting image with [Docker](http://docker.io) execute:

```
$ s2i build -e WAR_NAME=app.war -e INCREMENTAL=false git://github.com/bparees/openshift-jee-sample sarcouy/s2itomcat:8.5-jdk8-maven3.3.9 tomcattest
$ docker run -p 8080:8080 tomcattest
```

If you want to use an incremental build, don't forget -e `INCREMENTAL=true`

```
$ s2i build -e WAR_NAME=app.war -e INCREMENTAL=true git://github.com/bparees/openshift-jee-sample sarcouy/s2itomcat:8.5-jdk8-maven3.3.9 tomcattest
$ docker run -p 8080:8080 tomcattest
```

**Accessing the application:**
```
$ curl 127.0.0.1:8080
```


Repository organization
------------------------
* **`<Tomcat-version>`**
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

                  This script is responsible for running the application, by using the
                  Wildfly application server.

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

1. Platform version - 8
2. a dash "-"
3. Java version - jdk7
4. a dash "-"
5. Maven version - mvn3.3.3

Example: `sarcouy/s2itomcat:8-jdk8-mvn3.3.3`
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
    Set as "true" it avoid removing .m2 in between to build to allow lighter images. Must be used with `s2i build --incremental`
    Default to false

* WAR_NAME

    Name of the war file to move into webapps directory after maven build `WAR_NAME=myApp.war`


Copyright
--------------------

Released under the Apache License 2.0. See the [LICENSE](LICENSE) file.
