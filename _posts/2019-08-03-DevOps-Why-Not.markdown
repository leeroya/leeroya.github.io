---
layout: post
title:  "DevOps: Why not"
date:   2019-08-03 19:37:58 +0200
categories: DevOps
permalink: /devops-why-not/
---

I work in an awesome team of engineers, and our primary responsibility is to look after the systems for the company that build and deploy the software that is being created. On occasions I get seconded to other teams/departments in the company to assist with the respective pipelines and utterly enjoy working with other teams to see how the problems that were presented are solved, and how I can contribute in moving that software to production.

I had a discussion with an acquaintance not long ago and I was asked why would set up a automated build and how long does it take, here is a reply.

## Why 

The DevOps culture is like having a addiction, the more you work with it the more you want it to be everywhere. Around 4 years ago I started viewing the delivery of the software that our team then was deploying was inconsistent the delivery and configuration with made delivery very difficult as you had to follow a step by step a Word document and if something was not documented then the deployment usually failed.

## Where it began

Being introduced to [Octopus Deploy]("https://octopus.com/" target="_blank") was the first step to creating a enterprise grade deployment strategy for our applications as the company had already invested in the tool and it was just a case of learning how I could use it.

Not long after that I started working with the build systems for our software and was introduced to [TeamCity]("https://www.jetbrains.com/teamcity/" target="_blank"), after working with msbuild and other related build tasks we connected the two systems to allow packages to be created then consumed by octopus to deploy the packages to the servers.

## Fast Forward

Fast forward four years I am now working with TeamCity, Gitlab and Azure DevOps, using Octopus deploy and Docker to manage deployments, even private projects I run through automactic deployments, this website has a pipeline that uses [Gitlab]("https://gitlab.com/" target="_blank") and [Jekyll]("https://jekyllrb.com/" target="_blank") with [Docker]("https://docker.com/" target="_blank") to compile and deploy the website.

## The reason

The reason why I love the DevOps culture is apart from the union of developers and the software development cycle merging with the operational deployment and monitoring cycle, its the wholistic view, from writing code and seeing how that code gets converted into something a customer can see or use, making the cycle look alive.

## The work

When working or thinking in this way understand that it will always evolve and grow the same way that your code changes over time, start somewhere small like implementing a build workflow and grow it from there, the more you exercise this way of thinking the more it will be ingrained into your teams culture and how your company does work. Every journey starts with the first step, start your journey.

## Tools

This is based on your preference but some easy tools to use:

- Gitlab
- TeamCity
- Azure DevOps
- Docker
- Docker-compose
- PowerShell


## Conclusion

Now every chance that comes around when working with my code or other teams code, the first step is getting it compiling in a container, then take the artifact [out put of the build] and connect it to some deployment mechanism, then script all the in-between steps.

The goal to is have a traceable flow from commit through to production so that you know what is running in production.
