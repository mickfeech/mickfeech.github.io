---
layout: post
title: Can Chef and Docker Coexist?
modified:
categories: 
excerpt:
tags: [chef, docker, devops]
image:
  feature:
date: 2015-12-08T07:20:24-05:00
comments: true
---

As I've said in my previous posts, I've been researching many different tools in the space as it pertains to the practice of DevOps.  Docker is a no-brainer for the portability, scalability, and small footprint.  I've also been pretty impressed with Chef as an infrastructure as code / automation toolset, but can they coexist?  

Using Chef Machine to provision Docker containers is a pretty cool thing but does it have real world practicality when it comes to large scale deployment?  I'm really struggling to see it and it comes down to the way that chef licenses their product.  Unless I'm wrong (and my sales rep from Chef) they license per managed node.  The definition of a managed node was any node that would register back to the Chef Server.  The way I was initially going to provision the containers was have the chef-client bootstrap the container as a node and running as PID 1.  The chef-client would then register back to Chef Server, get the list of recipes, check it's run list, and then run the necessary recipes.  This is a great way to start using containers for legacy applications like a virtual machine; however sort of defeats the purpose of using the Docker engine for microservices.  As you begin to scale out your Docker infrastructure in this manner every one of those containers is licensable.  Great for Chef, not so much for enterprises.  

Another option is to bake chef-zero or chef-solo (a simple, easy-run, fast-start in-memory Chef server for testing and solo purposes) into a base OS Docker image.  You could then create a Dockerfile that used chef-zero in a similar fashion to apt or yum, but you would also have the luxury of chef being able to configure your service for you.  The other advantage, as I see it, is that you are now able to reuse those recipes for other deployment types (virtual machines, bare metal, and cloud).  So my thought is something like this...

~~~
FROM ubuntu:15.10
MAINTAINER me@someaddress.com
RUN curl -L https://www.chef.io/chef/install.sh | sudo bash
RUN echo "gem: --no-ri --no-rdoc" > ~/.gemrc
RUN /opt/chef/embedded/bin/gem install berkshelf
ADD . /chef
RUN cd /chef && /opt/chef/embedded/bin/berks install --path /chef/cookbooks
RUN chef-solo -c /chef/solo.rb -j /chef/solo.json
CMD["some_executable"]
~~~

What am I missing in the way that these tools can or should coexist?