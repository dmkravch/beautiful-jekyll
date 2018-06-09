---
layout: post
title: Hosting Webex Teams bots in Docker container on AWS AMI instance
subtitle: How to host a Webex Teams bot in a Docker container
tags: [development, webex teams, bots, aws, webex, docker]
---

This is a "How to" post on hosting Webex Teams bots.

In our team, we are developing variety of bots for different functions/departments. The easiest way for our "customers" to host those bots, is to use our teams servers. However, it becomes complicate to manages all those bots on the same server, with different dependencies and packages (also we are guilty of not using virtual env properly).

So, we've decided to try out Docker for hosting bots, with the further idea to transfer them to other servers.

In this post we will explore a procedure and caveats for a simple echo bot, just to show the methodology.

Let's get started then.

## Environment

We are using a AWS EC2 AMI Linux instance (t2.small).

The Docker was installed according to this doc [Docker Basics for Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html).

The bot's identity was created from [Webex Teams Developer Portal](https://developer.webex.com/apps.html). A very detailed step-by-stem on how to create it could be found at [Automating Webex Teams (Python)](https://learninglabs.cisco.com/tracks/collab-cloud/automating-spark-appdev/collab-spark-overview-rest/step/1) tutorial.

## Docker configuration

After reading this helpful post about [Building Minimal Docker Containers for Python Applications](https://blog.realkinetic.com/building-minimal-docker-containers-for-python-applications-37d0272c52f3) we've decided to use []**alpine**](https://github.com/gliderlabs/docker-alpine) image.
