
Docker allows programs to be shared across different operating systems and environments.  But it takes some learning to get it going.  

Something about union file system.
Something about Namespaces.

Fist thing you need to do is install it on your local system (this is going to be the host).  The install is found [at this link.](https://docs.docker.com/docker-for-mac/install/)

Once you've installed, follow the directions to install the Docker Desktop.  I think it really helps.  Plus, I have no idea how else to restart a stopped container.  So use the Desktop.  It also helps to visually see if you have your volumes mounted.

You need to see the freight-whale on the status bar next to zoom.  If you click on it, you can see a green dot to let you know its running.  You can also open the dashboard if you installed the desktop.  This is the gui that lets you see container, volume, image information.  Very useful stuff.

Some quick commands that will help you know docker is working:

```python
docker version
docker run hello-world
```

# Docker Image

An image is like a program.  You can download a program, but you still need to install it and run it.  This is the same workflow for an image.  

Install and image with pull
```
docker pull <image_name>
```
show all images with ls to see that the pull worked
```
docker image ls
```
You should see something like the following (image name is 'wernight/codiad' for this example).
```
REPOSITORY      TAG     IMAGE ID        CREATED         SIZE
wernight/codiad latest  8f0ac60a3629    2 years ago     506MB
```
This is equivalent to installing a program/app.  Now, to use it, we need to run it.  When we run an image, we spin up a container to do it.  These can be temporary, or we can come back to them later.  Due to their temporary nature, they get named arbitrary silly names if you don't name it yourself.

Here is a good sample run line from  [a good tutorial website](https://miykael.github.io/nipype_tutorial/notebooks/introduction_docker.html#:~:text=The%20%2D%2Drm%20flag%20tells,them%20accessible%20inside%20the%20container.):

```
docker run -it --rm 
-v /path_to/nipype_tutorial/:/home/nipype_tutorial 
-v /path/to/data/:/data 
-v /path/to/output/:/output 
-p 8888:8888 miykael/nipype_tutorial 
jupyter notebook
```
breaking it down:
docker run

-it = interactive mode --this will open shells if you are using sh or psql for example as your last arg

--rm = remove the container when docker is closed

-v = volume mounts or bind mounts. See volumes below for more.

-p = port locations.  The pattern is similarly -p <host_port>:<container_port>

jupiter notebook = the image name.  This might be postgres, ipython etc but you must end on the image tag. (not sure if -it is necessary with ipython...)

NOTE: when running exec command, you will call commands, not images.  You run an exec on a container name (image was already chosen at startup) and a command.  exec line looks like

## Run a docker image on Linux or Mac
Running a docker image on a Linux or Mac OS is very simple. Make sure that the folders tutorial, data, and output exist. Then just open a new terminal and use the command from above. Once the docker image is downloaded, open the shown URL link in your browser and you are good to go. The URL will look something like:

http://localhost:8888/?token=0312c1ef3b61d7a44ff5346d3d150c23249a548850e13868

make sure the port is the host port.  In this case they are the same, but that's not always the case.

For postgres, you won't run a URL. 

## Other image commands

* `docker pull <image_name>`: pull an image
* `docker image ls`: list all images
* `docker image history <image_name>`: list details of an image
* `docker image inspect <image_name>`: list details of an image
* `docker image rm <image_name>`: remove an image

# Docker Containers
Containers sit on top of images, or sometimes they are described as wrapping a thin layer around images. Here's a pic:

<img src="https://github.com/jigsawlabs-student/docker-intro/blob/master/1-docker-client/copy-on-write-container.png?raw=1" width="40%">

The container is in general temporary, though you can rerun one in the desktop. The container is what compartmentalizes images with volumes to allow you to save environments and such.

You can see a list of containers just like you see image list; with the ls command.
```
docker container ls
```
Here's an option I found from https://www.cloudbees.com/blog/docker-how-to-stop-and-remove-all-containers-at-once
```
docker ps -aq
```
The -aq option tells docker ps to list all containers (-a) by container ID (-q). You can combine the two arguments after a single dash (-).

Every time we use the run command, a new container is created.  

We boot up a container with:
```
docker run <image_name>
```
(notes say docker run container_name, but that's not right)

We execute a command in a container with:
```python
docker exec <container_name> command

#example
docker exec some-postgres5 psql -U jigsaw
#where you created this container using -e POSTGRES_USER=jigsaw in the run line
```
you may need to find the name from `docker container ls` or from docker desktop.

 We map the host's port to a container's port with:
 ```
 docker run -p host_port:container_port image_name
 ```
## Stop current container
 Stop a container with either `ctr+c` or :
 ```
docker stop <container_name>
 ```

Restart a container that has been stopped:
```
docker container start <name_of_previously_closed_container>
```

while the container is running, you should be able to work with it in a new terminal tab if you can't create commands because you have the container running in session. Otherwise you can create it with -d flag (daemon) and it will not run in the terminal (runs in the background).

## Stop all running containers
Sometimes you need to stop all containers. If you're running on a linux machine for example and can't use a docker desktop to see what's running and what's not.  Sometimes if you still have a container running, you may be calling an old image if you're not careful. A good way to clear out running containers is to stop them all then you can run a new fresh one without worry that your are calling an old out-dated image.

This is a great resource:
https://www.cloudbees.com/blog/docker-how-to-stop-and-remove-all-containers-at-once

Using these instructions, start by seeing all containers by id:
```
docker ps -aq
```

Force stop all containers:
```
$ docker ps -aq | xargs docker stop | xargs docker rm
```
If you want more info on these commands, see the link above.

If this still doesn't work (and it didn't for me when I was running in the AWS Workspace), there is a another 'less friendly' way but I think it's really a last resort thing. See the link above again if you want to read into why, but I had to do it and it worked, so use at your own peril.
```
$ docker ps -aq | xargs docker rm -f
```

## Volume mount and Bind mount

Mounting is done with the `-v` command and can be created with a new container, but CANNOT be connected to a current running container.

You can create a volume WITHOUT a container, though! so you can create the volume first, then mount it to a new container. No temp container needed!

Here is the command for creating a new independent volume:
```python
docker volume create <new_volume>
```
When creating a volume WITH the container creation (on the run line) it looks like this:

```python
docker run -it -v <name_vol>:/<container_data_location> 
```

 The pattern here is:
 
  `-v <vol_name>:<container_path>` for volume mount
 
 `-v <host_path>: <container_path> `for a bind mount
 
 A common bind mount is to use the present working directory ($PWD) or a folder connecting to it.  That would look like this:

 `-v $PWD:/home/jovyan/work `
 
 where pwd is the folder you are connecting from the host machine and  '/home/jovyan/work' is the folder you are connecting to on the container.


Note, you can have more than one -v command in a run, but -v can ONLY be used with run NOT with exec.  

IE, you set up your mounts at run/at container creation. You CANNOT mount a current running container. So if you've already created a volume and you want to mount to a new container, you run the same -v command.  It will use the current volume or create a new one if it doesn't already exist.

## Check your mounts/volumes
`[~]$ docker inspect jupyter-container -f '{{ .Mounts}}'`

Or just 

`[~]$ docker inspect jupyter-container`

which will be more verbose and harder to find the actual mount.

Of course, the docker desktop is a great tool for all of this if you can use it!

## This is all great, but needs to be updated with Dockerfile info
