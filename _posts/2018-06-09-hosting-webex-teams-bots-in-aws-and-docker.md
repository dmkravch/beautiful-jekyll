---
layout: post
title: Hosting Webex Teams bots in Docker container on AWS AMI instance
subtitle: How to host a Webex Teams bot in a Docker container
tags: [development, webex teams, bots, aws, webex, docker]
---

This is a "How to" post on hosting Webex Teams bots.

In our team, we are developing variety of bots for different functions/departments. The easiest way for our "customers" to host those bots, is to use our teams servers. However, it becomes complicate to manages all those bots on the same server, with different dependencies and packages (also we are guilty of not using virtual env properly).

So, we've decided to try out Docker for hosting bots, with the further idea to transfer them to other servers. 
