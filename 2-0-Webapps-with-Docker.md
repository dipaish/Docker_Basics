{% include topnav.html %}

## 2.0 Webapps with Docker
Great! So you have now looked at `docker run`, played with a Docker container and also got the hang of some terminology. Armed with all this knowledge, you are now ready to get to the real stuff &#8212; deploying web applications with Docker.

### 2.1 Run a website in a container

Let's start by taking baby-steps. First, we'll use Docker to run a static website in a container. The website is based on an existing image. We'll pull a Docker image from Docker Hub, run the container, and see how easy it is to set up a web server.


So, what happens when you run this command?


```bash
$ docker run -d nginxdemos/hello
```

Since the image doesn't exist on your Docker host, the Docker daemon first fetches it from the registry and then runs it as a container.

Now that the server is running, do you see the website? What port is it running on? And more importantly, how do you access the container directly from our host machine?

Actually, you probably won't be able to answer any of these questions yet! &#9786; In this case, the client didn't tell the Docker Engine to publish any of the ports, so you need to re-run the `docker run` command to add this instruction.

Let's re-run the command with some new flags to publish ports and pass your name to the container to customize the message displayed. We'll use the *-d* option again to run the container in detached mode.

First, stop the container that you have just launched. In order to do this, we need the container ID.

Since we ran the container in detached mode, we don't have to launch another terminal to do this. Run `docker ps` to view the running containers.

```bash
$ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
a7a0e504ca3e        dockersamples/static-site   "/bin/sh -c 'cd /usr/"   28 seconds ago      Up 26 seconds       80/tcp, 443/tcp     stupefied_mahavira
```

Check out the `CONTAINER ID` column. You will need to use this `CONTAINER ID` value, a long sequence of characters, to identify the container you want to stop, and then to remove it. The example below provides the `CONTAINER ID` on our system; you should use the value that you see in your terminal.

```bash
$ docker stop a7a0e504ca3e
$ docker rm   a7a0e504ca3e
```

>**Note:** A cool feature is that you do not need to specify the entire `CONTAINER ID`. You can just specify a few starting characters and if it is unique among all the containers that you have launched, the Docker client will intelligently pick it up.

Now, let's launch a container in **detached** mode as shown below:

```bash
$ docker run --name static-site -d -P nginxdemos/hello
```

In the above command:

* `--name` allows you to specify a container name 
* `-d` will create a container with the process detached from our terminal
* `-P` will publish all the exposed container ports to random ports on the Docker host



Now you can see the ports by running the `docker port` command. On your own host ports can be different so look at your own output of the command. 

```bash
$ docker port static-site
80/tcp -> 0.0.0.0:32773
```

You can also find port using Docker Desktop 


<img src="./assets/Comingsoon.png" title="Comingsoon"> 


You can now open browser to see your site live! For our example, this is: `http://localhost:32773`.


You can also run a second webserver at the same time, specifying a custom host port mapping to the container's webserver.

```bash
$ docker run --name static-site-2 -e AUTHOR="Your Name" -d -p 8888:80 dockersamples/static-site
```

<img src="./assets/Comingsoon.png" title="Comingsoon"> 

Now that you've seen how to run a webserver inside a Docker container, how do you create your own Docker image? This is the question we'll explore in the next section.

But first, let's stop and remove the containers since you won't be using them anymore.

```bash
$ docker stop static-site
$ docker rm static-site
```
Run `docker ps` to make sure the containers are gone.

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### 2.2 Docker Images

In this section, let's dive deeper into what Docker images are. You will build your own image, use that image to run an application locally, and finally, push some of your own images to Docker Cloud.

Docker images are the basis of containers. In the previous example, you **pulled** the *dockersamples/static-site* image from the registry and asked the Docker client to run a container **based** on that image. To see the list of images that are available locally on your system, run the `docker images` command.

```bash
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
nginx                  latest              af4b3d7d5401        3 hours ago        190.5 MB
alpine/git             latest              df6434eeda49        2 days ago         43.4MB

```

Above is a list of images that I've pulled from the registry and those I've created myself (we'll shortly see how). You will have a different list of images on your machine. The `TAG` refers to a particular snapshot of the image and the `ID` is the corresponding unique identifier for that image.

For simplicity, you can think of an image akin to a git repository - images can be [committed](https://docs.docker.com/engine/reference/commandline/commit/) with changes and have multiple versions. When you do not provide a specific version number, the client defaults to `latest`.

For example you could pull a specific version of `ubuntu` image as follows:

```bash
$ docker pull ubuntu:22.04
```

If you do not specify the version number of the image then, as mentioned, the Docker client will default to a version named `latest`.

So for example, the `docker pull` command given below will pull an image named `ubuntu:latest`:

```bash
$ docker pull ubuntu
```

To get a new Docker image you can either get it from a registry (such as the Docker Store) or create your own. There are hundreds of thousands of images available on [Docker Hub](https://hub.docker.com/). You can also search for images directly from the command line using `docker search`.

An important distinction with regard to images is between _base images_ and _child images_.

- **Base images** are images that have no parent images, usually images with an OS like ubuntu, alpine or debian.

- **Child images** are images that build on base images and add additional functionality.

Another key concept is the idea of _official images_ and _user images_. (Both of which can be base images or child images.)

- **Official images** are Docker sanctioned images. Docker, Inc. sponsors a dedicated team that is responsible for reviewing and publishing all Official Repositories content. This team works in collaboration with upstream software maintainers, security experts, and the broader Docker community. These are not prefixed by an organization or user name. In the list of images above, the `python`, `node`, `alpine` and `nginx` images are official (base) images. To find out more about them, check out the [Official Images Documentation](https://docs.docker.com/docker-hub/official_repos/).

- **User images** are images created and shared by users like you. They build on base images and add additional functionality. Typically these are formatted as `user/image-name`. The `user` value in the image name is your Docker Store user or organization name.

## Next Steps
For the next step in the tutorial, head over to 
[3.0 Docker shell](./3-0-Docker-Shell.md)
