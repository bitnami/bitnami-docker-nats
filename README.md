[![CircleCI](https://circleci.com/gh/bitnami/bitnami-docker-nats/tree/master.svg?style=shield)](https://circleci.com/gh/bitnami/bitnami-docker-nats/tree/master)

# What is NATS?

> NATS is an open-source, cloud-native messaging system.
> It provides a lightweight server that is written in the Go programming language.

[https://nats.io](https://nats.io/)

# TL;DR;

```bash
$ docker run -it --name nats bitnami/nats
```

## Docker Compose

```bash
$ curl -LO https://raw.githubusercontent.com/bitnami/bitnami-docker-nats/master/docker-compose.yml
$ docker-compose up
```

# Why use Bitnami Images?

* Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
* With Bitnami images the latest bug fixes and features are available as soon as possible.
* Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
* Bitnami images are built on CircleCI and automatically pushed to the Docker Hub.
* All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading linux distribution.

# Supported tags and respective `Dockerfile` links

* [`1-ol-7`, `1.2.0-ol-7-r4` (1/ol-7/Dockerfile)](https://github.com/bitnami/bitnami-docker-nats/blob/1.2.0-ol-7-r4/1/ol-7/Dockerfile)
* [`1-debian-9`, `1.2.0-debian-9-r6`, `1`, `1.2.0`, `1.2.0-r6`, `latest` (1/Dockerfile)](https://github.com/bitnami/bitnami-docker-nats/blob/1.2.0-debian-9-r6/1/Dockerfile)
* [`1-debian-8`, `1.2.0-debian-8-r4` (1/debian-8/Dockerfile)](https://github.com/bitnami/bitnami-docker-nats/blob/1.2.0-debian-8-r4/1/debian-8/Dockerfile)

Subscribe to project updates by watching the [bitnami/nats GitHub repo](https://github.com/bitnami/bitnami-docker-nats).

# Prerequisites

To run this application you need [Docker Engine](https://www.docker.com/products/docker-engine) >= `1.10.0`. [Docker Compose](https://www.docker.com/products/docker-compose) is recommended with a version `1.6.0` or later.

# Get this image

The recommended way to get the Bitnami NATS Docker Image is to pull the prebuilt image from the [Docker Hub Registry](https://hub.docker.com/r/bitnami/nats).

```bash
$ docker pull bitnami/nats:latest
```

To use a specific version, you can pull a versioned tag. You can view the
[list of available versions](https://hub.docker.com/r/bitnami/nats/tags/)
in the Docker Hub Registry.

```bash
$ docker pull bitnami/nats:[TAG]
```

If you wish, you can also build the image yourself.

```bash
$ docker build -t bitnami/nats:latest https://github.com/bitnami/bitnami-docker-nginx.git
```

# Connecting to other containers

Using [Docker container networking](https://docs.docker.com/engine/userguide/networking/), a NATS server running inside a container can easily be accessed by your application containers using a NATS client.

Containers attached to the same network can communicate with each other using the container name as the hostname.

## Using the Command Line

In this example, we will create a NATS client instance that will connect to the server instance that is running on the same docker network as the client.

### Step 1: Create a network

```bash
$ docker network create app-tier --driver bridge
```

### Step 2: Launch the NATS server instance

Use the `--network app-tier` argument to the `docker run` command to attach the NATS container to the `app-tier` network.

```bash
$ docker run -d --name nats-server \
    --network app-tier \
    --publish 4222:4222 \
    --publish 6222:6222 \
    --publish 8222:8222 \
    bitnami/nats:latest
```

### Step 3: Launch your NATS client instance

You can create a small script which downloads, installs and uses the [NATS Golang client](https://github.com/nats-io/go-nats).

There are some examples available to use that client. For instance, write the script below and save it as *nats-pub.sh* to use the publishing example:

```bash
#!/bin/bash

go get github.com/nats-io/go-nats
go build /go/src/github.com/nats-io/go-nats/examples/nats-pub.go
./nats-pub -s nats://nats-server:4222 "$1" "$2"
```

Then, you can use the script to create a client instance as shown below:

```bash
$ docker run -it --rm \
    --network app-tier \
    --volume /path/to/your/workspace:/go
    golang ./nats-pub.sh foo bar
```

## Using Docker Compose

When not specified, Docker Compose automatically sets up a new network and attaches all deployed services to that network. However, we will explicitly define a new `bridge` network named `app-tier`. In this example we assume that you want to connect to the NATS server from your own custom application image which is identified in the following snippet by the service name `myapp`.

```yaml
version: '2'

networks:
  app-tier:
    driver: bridge

services:
  nats:
    image: 'bitnami/nats:latest'
    ports:
      - 4222:4222
      - 6222:6222
      - 8222:8222
    networks:
      - app-tier
  myapp:
    image: 'YOUR_APPLICATION_IMAGE'
    networks:
      - app-tier
```

> **IMPORTANT**:
>
> 1. Please update the `YOUR_APPLICATION_IMAGE` placeholder in the above snippet with your application image
> 2. In your application container, use the hostname `nats` to connect to the NATS server

Launch the containers using:

```bash
$ docker-compose up -d
```

# Configuration

The configuration can easily be setup by mounting your own configuration file on the directory `/opt/bitnami/nats`:

```
docker run --name nats -v /path/to/gnatsd.conf:/opt/bitnami/nats/gnatsd.conf bitnami/nats:latest
```

After that, your configuration will be taken into account in the server's behaviour.

Using Docker Compose:

```yaml
version: '2'

services:
  nats:
    image: bitnami/nats:latest
    ports:
      - '4222:4222'
      - '6222:6222'
      - '8222:8222'
    volumes:
      - /path/to/gnatsd.conf:/opt/bitnami/nats/gnatsd.conf
```

Find more information about how to create your own configuration file on this [link](https://github.com/nats-io/gnatsd#configuration-file)

## Further documentation

For further documentation, please check [NATS documentation](https://nats.io/documentation/)

# Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/bitnami-docker-nats/issues), or submit a [pull request](https://github.com/bitnami/bitnami-docker-nats/pulls) with your contribution.

# Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/bitnami-docker-nats/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

# License

Copyright 2018 Bitnami

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
