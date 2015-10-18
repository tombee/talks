# Introduction to Docker Demos

## Container Demo

/Open two terminal windows, one for a container and one for running commands on the host OS.  Having two terminal windows makes comparisons between the two easier./

| Container | Host | Comment |
------------------------------
| `$ docker run -ti ubuntu bash` | | `create`s and then `start`s a Docker container from the `ubuntu` image |
| `$ ls -al /` | | We have a full Linux root file system |
| `$ cat /etc/os-release` | `$ cat /etc/os-release` | Shows that we have an Ubuntu root file system |
| `$ dpkg -l` | `$ dpkg -l` | Show that the container has its own set of debian packages |
| `$ dpkg -l | wc -l` | `$ dpkg -l | wc -l` | A lot fewer packages in the container than on the host |
| `$ ps faux` | `$ ps faux` | Only have the `bash` process that we started when we created the container, no Linux system services.  We don't have a full OS and init system running. |
| `$ ifconfig` | `$ ifconfig ` | Shows that the container has its own `eth0` and `lo` adapters, with their own addresses.  We actually see on the host a new `docker0` interface which is a virtual ethernet bridge.  Containers are created on their own subnet. |
| `ctrl-d` | | Close the ubuntu container |
| `$ docker run -ti centos bash` | | Show that we can start a centos flavoured image |
| `$ rpm -qa` | | Show that we have access to typical RedHat/CentOS system utils |

We can now see the containers that we have created and started:

```
$ docker ps -a
```

Linux distribution images are useful to us since they provide a root file system that contains the necessary configuration and system utils that we need to build and run an application.

It's not mandatory to use a linux distribution base image, some static binaries do not have any runtime package dependencies and can be executed from an empty container.

## Images Demo

/Open one terminal window and one browser window/

Running a Linux root filesystem in a container isn't all that interesting, so let's look at running an actual application in a container like nginx.

```
$ docker run -p 80:80 nginx
```

Open http://127.0.0.1 and see the default nginx index.html

Navigate to the Dockerfile and open it in a text editor, show that it's `FROM` nginx, the image that we ran earlier.

Build it:

```
$ docker build -t tombee/web:v1
```

Run it:

```
$ docker run -p 80:80 tombee/web:v1
```

Open http://127.0.0.1 and see "Hello World!"

Okay, so let's update it.. and release a new version.  Open Dockerfile and update "Hello World!" -> "Docker is awesome".

```
$ docker build -t tombee/web:v2
$ docker run -p 80:80 tombee/web:v2
```

Open http://127.0.0.1 and see "Docker is awesome!"

But.. it's too early to tell in our introduction that docker is awesome, so let's rollback to v1.

```
$ docker ps -a
$ docker start <id of v1 container>
```

Open http://127.0.0.1 and see "Hello World!"

This shows that we can rollback to a previous version, simply by keeping the old container and starting it up again.  No need to write downgrade scripts!

To get a list of the images on the host we can:

```
$ docker images
```

To inspect the list of commands that make up an image:

```
$ docker history tombee/web:v2
```

## Docker Hub demo

Open https://hub.docker.com/explore, show all of the current ~100 official images.
Open https://hub.docker.com/r/tombee, show personal repos

To push our newly built image:

```
$ docker push tombee/web:v2
```

Open https://hub.docker.com/r/tombee, see new web repo



