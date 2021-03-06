# Docker Couchbase Server

This is a Dockerfile and some supporting bits for running
[Couchbase Server](http://couchbase.com/) in a
[Docker](http://www.docker.io) container.

**NOTE**: *This project is currently being updated to use the new Docker
official Mac OS X support*.

There is a `Vagrantfile` in the `vagrant` directory that is configured to
prepare an Ubuntu 12.04 VirtualBox host for Docker and the requisite bits
needed by Couchbase Server, such as increasing open file limits and locked
memory as detailed below. If you use the supplied `Vagrantfile`, the steps
in the following section will be done for you when you `vagrant up`, and you
will not need to do them by hand.

## Prepare Docker Host

**Note about open file limits and locked memory**: You'll need to increase
the number of open files and locked memory available to Couchbase Server
containers from the Docker host.

To do so, edit `/etc/init/docker.conf` on the Docker host machine, and append
the following lines to the end of the file:

    limit memlock unlimited unlimited
    limit nofile 262144 262144

You'll need to restart the Docker daemon or host after making the above
changes. The above changes will affect the Docker daemon and all of its
children, including containers.

Prior to building and running the container, you'll need to prepare a
directory on the Docker host for mapping the Couchbase Server data
directory if you wish to preserve your data through restarts of the image.

    sudo mkdir /home/couchbase-server
    sudo chown 999:999 /home/couchbase-server

## Build & Run a Container

Now you can build and run a Couchbase Server Docker container; be sure to
replace `<yourname>` in the example shown below with your own unique
identifier.

The following command will build the container:

    cd docker-couchbase-server
    sudo docker build -t <yourname>/couchbase-server .

A command like the following is used to run the container:

    sudo docker run -i -t  -p 11210:11210 -p 8091:8091 -p 8092:8092 \
    <yourname>/couchbase-server

or, if you prefer to have the container run in the background:

    sudo docker run -i -t -d -p 11210:11210 -p 8091:8091 -p 8092:8092 \
    <yourname>/couchbase-server

## Mac OS X Native Docker Client Instructions

To use this project on Mac OS X with official Docker support and the
`boot2docker` tool, follow these instructions:

Get the `boot2docker` script:

    cd ~/bin
    curl https://raw.github.com/steeve/boot2docker/master/boot2docker > boot2docker
    chmod +x boot2docker

Learn more about Docker Mac OS X support in the
[Mac OS X installation](http://docs.docker.io/en/latest/installation/mac/)
documentation

Once you've installed `boot2docker` you can download and install the latest
Docker Mac OS X client:

    curl -o docker http://get.docker.io/builds/Darwin/x86_64/docker-latest
    chmod +x docker
    # replace 'localhost' below with Docker server hostname if needed
    export DOCKER_HOST=tcp://localhost
    sudo cp docker /usr/local/bin/

Now initialize and boot the Docker daemon and VM:

    ~/bin/boot2docker init
    ~/bin/boot2docker up

Using the Docker client will now work as expected agains the VirtualBox
based VM host:

    docker version

You can also easily ssh into the VM:

    ~/bin/boot2docker ssh

* User name: *root*
* Passowrd: *tcuser*

Once you've established the Docker host, you can follow the directions under
**Prepare Docker Host** and **Build & Run a Container** to get the project
up and running.
