---
layout: post
title: Goodbye FreeNAS
modified:
categories: 
excerpt:
tags: [freenas, docker, ubuntu, zfs]
image:
  feature:
date: 2015-11-29T19:42:29-05:00
comments: true
---

Last year I took the FreeNAS plunge with some older hardware I had around the house and for all intents and purposes I was quite happy with my initial decision to use it.  FreeNAS is based on the FreeBSD operating system and it leverages the [ZFS file system](https://en.wikipedia.org/wiki/ZFS); it's the biggest feature is protection against data corruption (think software RAID) and supports large storage pools.  FreeNAS also offers up a jail container system which is really good for running software that many be associated with your NAS; for instance I ran my plex server out of a jail.  As time passed I was becoming frustrated with not having certain capabilities available to me and was tired of waiting on the FreeNAS developers to update to their newest version.  

By chance I was reading the documentation for the latest version Ubuntu (15.10) and saw that it was going to natively support ZFS, excellent I thought.  I put my nose down and started doing some research on whether the native ZFS support would allow me to just drop my drives in and go.  Unfortunately, I never found a clear answer and started to backup all my data in the event I had to copy it back.  I installed Ubuntu server on a flash drive and went to work trying to get my data without having to copy it back in.  Anyone looking to do this in the near term should be aware that ZFS-FUSE will not allow you to import the existing FreeBSD pools into Ubuntu.  I did some more research and found the [ZFS on Linux (ZOL) project](http://zfsonlinux.org/) and found that many were able to do exactly what I was looking for.  That team provides a PPA so that you can do an ``apt-get install ubuntu-zfs``; however with this version of Ubuntu the repository hasn't been updated.  In order to get around this you actually need to modify the repo list from wily to vivid (the previous release) otherwise if won't find the package

~~~
deb http://ppa.launchpad.net/zfs-native/stable/ubuntu vivid main
#deb http://ppa.launchpad.net/zfs-native/stable/ubuntu wily main
~~~

I did my ``apt-get install ubuntu-zfs`` and it just picked up my existing ZFS pools.  Awesome. My next task was to see if I could dockerize my plex install.  Thankfully this wasn't too bad.  I created a base OS image that I'll be able to reuse later and then created this Dockerfile

~~~
FROM docker-base:latest
MAINTAINER mickfeech@gmail.com

ENV PLEX_VERSION 0.9.14.4.1556-a10e3c2
ENV PLEX_FILE plexmediaserver_0.9.14.4.1556-a10e3c2_amd64.deb
RUN curl https://downloads.plex.tv/plex-media-server/$PLEX_VERSION/$PLEX_FILE > /tmp/plex.deb
RUN dpkg -i /tmp/plex.deb

ADD ./start.sh /start.sh
RUN chmod u+x  /start.sh

ADD ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf

ENV RUN_AS_ROOT="true"
ENV CHANGE_DIR_RIGHTS="false"

EXPOSE 32400

CMD ["/start.sh"]
~~~

The start.sh just uses supervisor to ensure that the process is running.  I spent the largest amount of time trying to figure out if I could use docker network to create a bridge that would give me an external IP.  After about two hours, I decided to just add a secondary IP on the existing interface and I was able to start up my docker container with a static IP.  The next bit of business will be creating docker containers for other things that I was running in the FreeNAS jails.  But that will be for another time. 