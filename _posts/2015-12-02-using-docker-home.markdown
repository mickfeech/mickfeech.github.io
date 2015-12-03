---
layout: post
title: Using Docker@home
modified:
categories: 
excerpt:
tags: [docker, containers]
image:
  feature:
date: 2015-12-02T19:43:59-05:00
comments: true
---

One of the great things about Docker is the portability and confidence that the consistency provides.  There are some things you need to consider before leveraging Docker for your use (personal or professional), however.  Docker and the tools around them are pretty volatile.  Now I don't mean that in a negative way at it; it's great that it's changing and innovating.  The new network stack within Docker is a great example.  

For those that don't know (or are unaware); Docker completely changed its network stack (utilizing libnetwork), it was considered generally available this November, to offer more extensibility are start moving it down the path of something of an SDN.  You can now choose between different network drivers, based on your use case.  The four that it ships with are bridge, host, overlay, and none.  

* None is pretty self-explanatory, it adds a container to a container-specific network stack that lacks a network interface, great for containers that need to be completely isolated, for whatever reason.  
* The host network adds a container on the host's network stack and the network configurations inside the container will be a mirror of the configuration on the Docker host.
* The bridge driver, which is the default, is similar to the default docker0 network of previous versions.  The containers you launch into this network must reside on the same Docker host.  The power with this network driver is each container within the network can immediately communicate with other containers in the same network, no linking necessary.  The caveat is the network isolates the containers from external networks (more on this later).  A bridge network is useful when you want to run a relatively small network on a single host
* The overlay driver supports multi-host networking natively.  The caveat here is that it requires a KV store service (currently supports consul, etcd, and ZooKeeper) and it must be installed and configured before creating the network.  It should go without saying that the hosts you intend on networking together must be able to communicate via those services as well (firewall ports opened).  Once connected, each container has access to all the containers in the network regardless of which Docker host the container was launched on.

Here's where the volatility comes into play... it is my opinion that by and large the documentation is complete; however examples in the community seem to be lacking.  Currently getting external IP addresses for Docker containers is not as intuitive as it could be.  I would really like the Docker network stack to be able to assign through defined ranges or have the IP address assigned by network devices that would allow devices outside of the host to connect to it.  If I want to accomplish this today I have to assign it a secondary IP address on my current interface, for example, ```ip addr add 192.168.1.32/24 dev eno1``` unfortunately this makes ```ip addr show``` someone messy to read if you want to add a lot of containers as "standalone" hosts (see snippet below).

~~~
inet 192.168.0.30/24 scope global secondary eno1
   valid_lft forever preferred_lft forever
inet 192.168.0.32/24 scope global secondary eno1
   valid_lft forever preferred_lft forever
inet 192.168.0.34/24 scope global secondary eno1
   valid_lft forever preferred_lft forever
~~~

After the IPs have been added I then need to make sure as I'm running the docker run command (or compose file) I'm passing it the virtual IP I want it to use the -p parameter, for instance, ```docker run -d -p 192.168.0.34:32400:32400 plex:latest```.

I've also started playing with docker-compose, finally.  It essentially allows you to orchestrate the building, running, and stopping of containers through a YAML file.  Currently, you still have to link your containers unless you pass it ```--x-networking``` flag during your up command, as it's experimental.   

*[SDN]: Software Defined Network
*[KV]: Key-Value
*[YAML]: YAML Ain't Markup Language