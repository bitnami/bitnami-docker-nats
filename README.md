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
* All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading linux distribution.
* All Bitnami images available in Docker Hub are signed with [Docker Content Trust (DTC)](https://docs.docker.com/engine/security/trust/content_trust/). You can use `DOCKER_CONTENT_TRUST=1` to verify the integrity of the images.
* Bitnami container images are released daily with the latest distribution packages available.


> This [CVE scan report](https://quay.io/repository/bitnami/nats?tab=tags) contains a security report with all open CVEs. To get the list of actionable security issues, find the "latest" tag, click the vulnerability report link under the corresponding "Security scan" field and then select the "Only show fixable" filter on the next page.

# How to deploy NATS in Kubernetes?

Deploying Bitnami applications as Helm Charts is the easiest way to get started with our applications on Kubernetes. Read more about the installation in the [Bitnami NATS Chart GitHub repository](https://github.com/bitnami/charts/tree/master/upstreamed/nats).

Bitnami containers can be used with [Kubeapps](https://kubeapps.com/) for deployment and management of Helm Charts in clusters.

# Why use a non-root container?

Non-root container images add an extra layer of security and are generally recommended for production environments. However, because they run as a non-root user, privileged tasks are typically off-limits. Learn more about non-root containers [in our docs](https://docs.bitnami.com/containers/how-to/work-with-non-root-containers/).

# Supported tags and respective `Dockerfile` links

> NOTE: Debian 8 images have been deprecated in favor of Debian 9 images. Bitnami will not longer publish new Docker images based on Debian 8.

Learn more about the Bitnami tagging policy and the difference between rolling tags and immutable tags [in our documentation page](https://docs.bitnami.com/containers/how-to/understand-rolling-tags-containers/).


* [`2-ol-7`, `2.1.2-ol-7-r21` (2/ol-7/Dockerfile)](https://github.com/bitnami/bitnami-docker-nats/blob/2.1.2-ol-7-r21/2/ol-7/Dockerfile)
* [`2-debian-9`, `2.1.2-debian-9-r14`, `2`, `2.1.2`, `2.1.2-r14`, `latest` (2/debian-9/Dockerfile)](https://github.com/bitnami/bitnami-docker-nats/blob/2.1.2-debian-9-r14/2/debian-9/Dockerfile)
* [`2-photon-3`, `2.0.0-photon-3-r0` (2/photon-3/Dockerfile)](https://github.com/bitnami/bitnami-docker-nats/blob/2.0.0-photon-3-r0/2/photon-3/Dockerfile)

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
$ docker build -t bitnami/nats:latest 'https://github.com/bitnami/bitnami-docker-nginx.git#master:2/debian-9'
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
docker run --name nats -v /path/to/nats-server.conf:/opt/bitnami/nats/nats-server.conf bitnami/nats:latest
```

After that, your configuration will be taken into account in the server's behaviour.

You can also do this by modifying the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-nats/blob/master/docker-compose.yml) file present in this repository:

```yaml
services:
  nats:
  ...
    volumes:
      - /path/to/nats-server.conf:/opt/bitnami/nats/nats-server.conf
  ...
```

Find more information about how to create your own configuration file on this [link](https://nats-io.github.io/docs/nats_server/configuration.html)

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

Copyright 2019 Bitnami

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
