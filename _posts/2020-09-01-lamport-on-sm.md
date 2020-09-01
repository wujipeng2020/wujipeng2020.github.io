---
layout: post
title: "Lamport的分布式状态机论述"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Lamport说：
> A distributed system can be described as a particular sequential state machine that is implemented with a network of processors. The ability to totally order the input requests leads immediately to an algorithm to implement an arbitrary state machine by a network of processors, and hence to implement any distributed system. So,I wrote this paper, which is about how to implement an arbitrary distributed state machine. As an illustration, I used the simplest example of a distributed system I could think of--a distributed mutual exclusion algorithm.