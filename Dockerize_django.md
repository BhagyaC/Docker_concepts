# How to dockerize  a Django app

This is all about how to package and distribute an API build in Django and Django REST Framework using docker containers.

Docker is an open platform that performs Operating System level virtualization also known as containerization.




**A container image is a lightweight, stand-alone, executable package of a piece of software that includes everything needed to run it: code, runtime, system tools, system libraries, settings. Available for both Linux and Windows based apps, containerized software will always run the same, regardless of the environment. Containers isolate software from its surroundings, for example differences between development and staging environments and help reduce conflicts between teams running different software on the same infrastructure — Docker documentation.**

Just open the project where the Django app created in a text editor. Then create a docker file.
Docker file is a list of instruction which docker will follow while creating our image.

Docker has two stages one is a build stage and another is run stage. On the build stage build the container image. It looks the docker file and creates an image and runs all of the commands in the docker file. 

Add a file named `Dockerfile` without any extension. 
The few to commands should run so that to prepare our image. One intresting thoughts of docker, we can arrange the images as layers.

We are using a python based image. `FROM python:3`. Here we are using official python as a parent image. Next thing is to create a env `ENV PYTHONUNBUFFERED 1` .Here the environment variable is set ehich instruct the Docker not to  buffer the output from buffer from the standard output buffer, but simply send it straight to the terminal.  We want to create a directory is called for the source code. The folder will be created in the container.
`RUN mkdir /code`.

Next we have to create a working directory `WORKDIR /code` . Now we want to copy all the files for our project to our container.
Now the conatainer doesnot contain any source code. We have to copy the code from the local using the COPY commamd `COPY . /code/` means all files from the local to the code directory named code in the container.

Now we have to run all the requirements wich will install the requirement for our project.
`RUN pip install -r requirements.txt`
## Docker Compose

Compose is a tool for defining and running multi-container Docker applications. — Docker documentation
So for a simple docker file this is enough. Next we have to compose the docker compose config. That is nothing but a `docker-compose.yml` file.

In the docker-compose we have to specify which version of the docker compose syntax we are using. We are using the docker-compose version 3 .

So add `version:'3'` .Now we can define the services.
```
services:
    web:
      build: .
      command: python <path to migrate>/manage.py runserver 0.0.0.0:8000 # path of the manage file 
      volumes: 
        - .:/code  #maps the volume from the local volume to the docker.when a change is made in the source code it will simultanously  change the code in the docker container.
      ports:
        - "8000:8000" # we are creating a port from our container to our local machine. Here in the example when our running our devolopment server port 8000 it will map that to our host machine. When we have access to port 8000 to the host machine will will fold our the connection to docker container.
```   
Next is set up out database for our for project.
So add a migration command to migrate our project.So run the command in the terminal `docker-compose run web python <path to migrate>/manage.py migrate` this command just create a docker container for us.
        you can also manually build the container using the command in the terminal `docker-compose build` you can see that its running everything that we defined. Now the image is create it `docker-compose up` atomatically will build it.
