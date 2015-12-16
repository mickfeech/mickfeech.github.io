---
layout: post
title: Swarm, Kubernetes, or Mesos?
modified:
categories: 
excerpt:
tags: [devops, docker, mesos, kubernetes]
image:
  feature:
date: 2015-12-15T21:27:13-05:00
comments: true
---

Docker by itself is great if you need single containers and you don't mind manual scaling.  The new network (see my [comments](http://mickfeech.github.io/using-docker-home/) around the new network layer) stack within Docker helps a little in this end; however fundamentally you'd either have to build your own automation / mechanism or use something that already exists.  I wouldn't want to spend my time building something when there seem to be perfectly good solutions out there already.  The three main solutions that most people talk about are Docker Swarm, Google Kubernetes, or Apache Mesos.  I'll also say that this space seems pretty volatile with new options or updates coming out daily.

Apache Mesos is fairly mature outside of container orchestration.  The secret sauce for Mesos comes into play if you have existing workloads that span multiple machines, datacenters, or clouds.  It's able to schedule, scale, and prioritize everything from containers to physical hardware.  It's even able to do that with Kubernetes or Docker Swarm.  I hate to make the comparison or use the term but it could be an orchestrator of orchestrators or it could stand alone by itself.  When I was at DevOpsDays in Ohio my perception was that there a greater use of Mesos than Kubernetes.  I don't know if that perception was real or imagined (because there wasn't much discussion going on around Kubernetes).

The abstraction of container technology that Google provides, in Kubernetes, makes it seem like it has the advantage over Swarm.  In theory, one could use any container technology with Kubernetes although the default is Docker (I believe).  I also don't think anyone can argue that Google knows how to scale, manage, and automate their technology.  They are obviously doing it right and I'm not the only one that thinks so.  It seems to me that RedHat has gone "all-in" building their new OpenShift (3 / Origin) platform using Kubernetes and Docker (there is also some heavy integration for OpenStack). For some historical context, for those unaware, their previous OpenShift PaaS utilized CloudFoundry.  A number of other PaaS solutions are built using [CloudFoundry](https://www.cloudfoundry.org/), including [IBM's BlueMix](http://www.ibm.com/cloud-computing/bluemix/) and [Pivotal (EMC / VMware) CloudFoundry](http://pivotal.io/platform).  RedHat hadn't joined the [CloudFoundry Foundation](https://www.cloudfoundry.org/foundation/), I guess we know why now.  At the heart of Kubernetes, everything is done with through the CLI; this may turn some off, although it shouldn't (you've automated things, right?).  The initial setup could be considered complicated.  I've looked at it myself on a number of occasions and have gotten lost or things just didn't work without manual tweaking.

Docker Swarm provides native clustering in Docker.  In theory, it should have a leg up on the competition when all is said and done it is their underlying API after all.  I'll admit that I haven't tackled trying to set up Swarm, yet.  However, everything that I've read suggests that the setup of Swarm is significantly easier than Kubernetes.  It looks to be as easy as installing one of the discovery tools and running a swarm container on all the hosts.  

I guess time will tell which tool will eventually win out.  In the meantime, I'll continue to keep my eyes open for comments and opinions throughout the blogs, papers, and social media to see how things mature and what others are doing.


*[PaaS]: Platform as a Service
*[CLI]: Command Line Interface.