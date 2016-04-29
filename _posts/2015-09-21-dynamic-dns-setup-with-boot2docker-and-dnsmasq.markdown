---
layout: post
title:  "Setting up dynamic hostnames with dnsmasq for docker containers running on boot2docker"
date:   2015-09-21 21:00:00
categories: docker
tags: docker dns dnsmasq booot2docker
---

At work we use [Docker](http://www.docker.com) for setting up app
environments both in production and development.  Docker support is
great on Linux, but on OS X the only available solution at the moment
is to run [boot2docker](http://www.boot2docker.io).
[Docker machine](https://docs.docker.com/machine/) is underway, but it
looks like it will have some of the same shortcomings as with
boot2docker.  When running docker-containers on Linux, a container is
reachable directly on localhost, no middleman required, but on OS X
it's a different story. There, every container is running inside the
boot2docker host, and without some virtualbox magic, containers aren't
reachable through localhost at all. Given this situation, I started
thinking that maybe there is a solution that will be even more
flexible than mapping everything to localhost via virtualbox port
forwarding. Another trick is container-linking, but that doesn't
really help if you want to connect to the container from something
that isn't a docker-container in a reliable way (eg with
[pgAdmin](http://pgadmin.org/)).

I did some googling and came across the awesome application
[dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html). It's
basically a local DNS-server that can override certain hosts to be
routed to custom ip-addresses.

An example use case: I would like to install a postgresql database
container, run it in boot2docker and to be able to connect to it using
a custom domain say `local_postgres.dev`.

## Setup

First of all I assume that boot2docker and homebrew is up and
running.  We begin by installing `dnsmasq` with homebrew. Open a terminal and run:

```sh
$ brew up
$ brew install dnsmasq
```

Follow the instructions from homebrew to setup the relevant config files for dnsmasq.
The next step is to boot the postgresql docker container:

```sh
$ docker run --name local_postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
```

This command pulls down a docker container running postgresql with username: postgres and password: mysecretpassword
