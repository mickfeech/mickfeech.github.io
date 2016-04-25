---
layout: post
title: Bimodal IT, is it sustainable?
modified:
categories:
excerpt:
tags: [devops, bimodal]
comments: true
image:
  feature:
date: 2016-04-25T10:00:00-05:00
---

[Gartner](http://www.gartner.com/technology/home.jsp) has come up with a term called "Bimodal IT" or sometimes referred to "Two-Speed IT" in many of their DevOps research topics.  Their definition of this term as:

> The practice of managing two separate, coherent modes of IT delivery, one focused on stability and the other on agility. Mode 1 is traditional and sequential, emphasizing safety and accuracy.  Mode 2 is exploratory and nonlinear, emphasizing agility and speed.

Okay, great, that all makes sense in my head.  Based on everything I've read (admittedly, only a few articles) from Gartner on the subject, the assumption is that this model goes on indefinitely.  There is no way that this can be sustainable.  Jez Humble agrees as he writes in his latest blog post ["The flaw at the heart of bimodal IT"](http://continuousdelivery.com/2016/04/the-flaw-at-the-heart-of-bimodal-it/):

> There are three serious problems with the Bimodal model which, when taken together, mean that leaders that fail to move beyond Gartner’s advice will end up falling further and further behind the competition. They will continue to invest ever more money to maintain systems that will become increasingly complex and fragile over time, while failing to gain the expected return on investment from adopting agile methods.

So what good is bimodal IT?  Well, I think it is a means to an end.  There's no way large organizations can change all of their delivery at once.  So bimodal IT can help make incremental changes.  Essentially mode 2 overtakes mode 1 via the [strangler pattern](http://schmij.github.io/Strangler-Pattern/).  Now the biggest problem enterprises will face as they begin to shift from mode 1 to mode 2 will be how quickly the strangler pattern takes over "legacy".  If they don't shift their delivery models quickly that is where they will just throw resources away.  If an enterprise will take the plunge into mode 2 they need to have and stick to timelines to eventually stop delivering with mode 1.

Any large organizations that are claiming success with bimodal IT are likely only short term successes and it is likely only that they are doing it.  What do their budgets look like support two IT organizations?  What does they look like in 3-4 years?  I have to imagine that if they are supporting two completely different IT organizations their budgets would inflate significantly.
