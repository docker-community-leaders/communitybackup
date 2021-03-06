# Run a Docker Container

The first step in running an application using Docker is to run a container. If you can think of an open source software, there is a very high likelihood that there will be a Docker image available for it at https://store.docker.com[Docker Store]. Docker client can simply run the container by giving the image name. The client will check if the image already exists on Docker Host. If it exists then it'll run the container, otherwise the host will first download the image.

## Pull Image

Let's check if any images are available:

```
docker image ls
```

At first, this list is empty. If you've already downloaded the images as specified in the setup chapter, then all the images will be shown here. 

List of images can be seen again using the `docker image ls` command. This will show the following output:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
hellojava                    latest              8d76bf5691c4        32 minutes ago      740MB
hello-java                   latest              93b1180c5d91        36 minutes ago      740MB
helloworld                   2                   7fbedda27c66        41 minutes ago      1.13MB
helloworld                   latest              e61f88f3a0f7        About an hour ago   122MB
mysql                        latest              b4e78b89bcf3        3 days ago          412MB
ubuntu                       latest              2d696327ab2e        4 days ago          122MB
jboss/wildfly                latest              9adbdb00cded        8 days ago          592MB
openjdk                      latest              6077adce18ea        8 days ago          740MB
busybox                      latest              54511612f1c4        9 days ago          1.13MB
tailtarget/hadoop            2.7.2               ee6b539c886e        6 months ago        1.15GB
tailtarget/jenkins           2.32.3              71a7d9bcfe2b        6 months ago        859MB
arungupta/couchbase          travel              7929a80707db        7 months ago        583MB
arungupta/couchbase-javaee   travel              2bb52abaad5f        7 months ago        595MB
arungupta/javaee7-hol        latest              da5c9d4f85ca        2 years ago         582MB
```

More details about the image can be obtained using `docker image history jboss/wildfly` command:

```
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
9adbdb00cded        8 days ago          /bin/sh -c #(nop)  CMD ["/opt/jboss/wildfl...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  EXPOSE 8080/tcp              0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  USER [jboss]                 0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  ENV LAUNCH_JBOSS_IN_BAC...   0B                  
<missing>           8 days ago          /bin/sh -c cd $HOME     && curl -O https:/...   163MB               
<missing>           8 days ago          /bin/sh -c #(nop)  USER [root]                  0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  ENV JBOSS_HOME=/opt/jbo...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  ENV WILDFLY_SHA1=9ee3c0...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  ENV WILDFLY_VERSION=10....   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  ENV JAVA_HOME=/usr/lib/...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  USER [jboss]                 0B                  
<missing>           8 days ago          /bin/sh -c yum -y install java-1.8.0-openj...   204MB               
<missing>           8 days ago          /bin/sh -c #(nop)  USER [root]                  0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  MAINTAINER Marek Goldma...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  USER [jboss]                 0B                  
<missing>           8 days ago          /bin/sh -c #(nop) WORKDIR /opt/jboss            0B                  
<missing>           8 days ago          /bin/sh -c groupadd -r jboss -g 1000 && us...   296kB               
<missing>           8 days ago          /bin/sh -c yum update -y && yum -y install...   28.7MB              
<missing>           8 days ago          /bin/sh -c #(nop)  MAINTAINER Marek Goldma...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           8 days ago          /bin/sh -c #(nop)  LABEL name=CentOS Base ...   0B                  
<missing>           8 days ago          /bin/sh -c #(nop) ADD file:1ed4d1a29d09a63...   197MB               
```

## Run Container

### Interactively

Run WildFly container in an interactive mode.

```
docker container run -it jboss/wildfly
```

This will show the output as:

```
=========================================================================

  JBoss Bootstrap Environment

  JBOSS_HOME: /opt/jboss/wildfly

  JAVA: /usr/lib/jvm/java/bin/java

. . .

00:26:27,455 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0060: Http management interface listening on http://127.0.0.1:9990/management
00:26:27,456 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0051: Admin console listening on http://127.0.0.1:9990
00:26:27,457 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0025: WildFly Full 10.1.0.Final (WildFly Core 2.2.0.Final) started in 3796ms - Started 331 of 577 services (393 services are lazy, passive or on-demand)
```

This shows that the server started correctly, congratulations!

By default, Docker runs in the foreground. `-i` allows to interact with the STDIN and `-t` attach a TTY to the process. Switches can be combined together and used as `-it`.

Hit Ctrl+C to stop the container.

=== Detached container

Restart the container in detached mode:

```
docker container run -d jboss/wildfly
254418caddb1e260e8489f872f51af4422bc4801d17746967d9777f565714600
```

`-d`, instead of `-it`, runs the container in detached mode.

The output is the unique id assigned to the container. Logs of the container can be seen using the command `docker container logs <CONTAINER_ID>`, where `<CONTAINER_ID>` is the id of the container.

Status of the container can be checked using the `docker container ls` command:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
254418caddb1        jboss/wildfly       "/opt/jboss/wildfl..."   2 minutes ago       Up 2 minutes        8080/tcp            gifted_haibt
```

Also try `docker container ls -a` to see all the containers on this machine.

=== With default port

If you want the container to accept incoming connections, you will need to provide special options when invoking `docker run`. The container, we just started, can't be accessed by our browser. We need to stop it again and restart with different options.

```
docker container stop `docker container ps | grep wildfly | awk '{print $1}'`
```

Restart the container as:

```
docker container run -d -P --name wildfly jboss/wildfly
```

`-P` map any exposed ports inside the image to a random port on Docker host. In addition, `--name` option is used to give this container a name. This name can then later be used to get more details about the container or stop it. This can be verified using `docker container ls` command:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
89fbfbceeb56        jboss/wildfly       "/opt/jboss/wildfl..."   9 seconds ago       Up 8 seconds        0.0.0.0:32768->8080/tcp   wildfly
```

The port mapping is shown in the `PORTS` column. Access WildFly server at http://localhost:32768. Make sure to use the correct port number as shown in your case.

NOTE: Exact port number may be different in your case.

The page would look like:

![My Image)(wildfly-first-run-default-page.png)

## With specified port

Stop and remove the previously running container as:

```
docker container stop wildfly
docker container rm wildfly
```

Alternatively, `docker container rm -f wildfly` can be used to stop and remove the container in one command. Be careful with this command because `-f` uses `SIGKILL` to kill the container.

## Restart the container as:

```
docker container run -d -p 8080:8080 --name wildfly jboss/wildfly
```

The format is `-p hostPort:containerPort`. This option maps a port on the host to a port in the container. This allows us to access the container on the specified port on the host.

Now we're ready to test http://localhost:8080. This works with the exposed port, as expected.

Let's stop and remove the container as:

```
docker container stop wildfly
docker container rm wildfly
```

## Deploy a WAR file to application server

Now that your application server is running, lets see how to deploy a WAR file to it.

Create a new directory `hellojavaee`. Create a new text file and name it `Dockerfile`. Use the following contents:

```
FROM jboss/wildfly:latest

RUN curl -L https://github.com/javaee-samples/javaee7-simple-sample/releases/download/v1.10/javaee7-simple-sample-1.10.war -o /opt/jboss/wildfly/standalone/deployments/javaee-simple-sample.war
```

## Create an image:

```
docker image build -t javaee-sample .
```

Start the container:

```
docker container run -d -p 8080:8080 --name wildfly javaee-sample
```

## Access the endpoint:

```
curl http://localhost:8080/javaee-simple-sample/resources/persons
```

See the output:

```
<persons>
	<person>
		<name>
		Penny
		</name>
	</person>
	<person>
		<name>
		Leonard
		</name>
	</person>
	<person>
		<name>
		Sheldon
		</name>
	</person>
	<person>
		<name>
		Amy
		</name>
	</person>
	<person>
		<name>
		Howard
		</name>
	</person>
	<person>
		<name>
		Bernadette
		</name>
	</person>
	<person>
		<name>
		Raj
		</name>
	</person>
	<person>
		<name>
		Priya
		</name>
	</person>
</persons>
```

Optional: `brew install XML-Coreutils` will install XML formatting utility on Mac. This output can then be piped to `xml-fmt` to display a formatted result.

## Stop container

Stop a specific container by id or name:

```
docker container stop <CONTAINER ID>
docker container stop <NAME>
```

## Stop all running containers:

```
docker container stop $(docker container ps -q)
```

## Stop only the exited containers:

```
docker container ps -a -f "exited=-1"
```

## Remove container

Remove a specific container by id or name:

``
docker container rm <CONTAINER_ID>
docker container rm <NAME>
```

## Remove containers meeting a regular expression

```
docker container ps -a | grep wildfly | awk '{print $1}' | xargs docker container rm
```

## Remove all containers, without any criteria

```
docker container rm $(docker container ps -aq)
```

##  Additional ways to find port mapping

The exact mapped port can also be found using `docker port` command:

```
docker container port <CONTAINER_ID> or <NAME>
```

This shows the output as:

```
8080/tcp -> 0.0.0.0:8080
```

Port mapping can be also be found using `docker inspect` command:

```
docker container inspect --format='{{(index (index .NetworkSettings.Ports "8080/tcp") 0).HostPort}}' <CONTAINER ID>
```
