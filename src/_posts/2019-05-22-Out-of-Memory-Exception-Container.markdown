---
layout: post
title:  "Windows Container Unhandled Exception: OutOfMemoryException"
date:   2019-05-22 07:00:58 +0200
categories: docker
---

### Windows Container Unhandled Exception: OutOfMemoryException

I was creating a pipeline for a Azure Service Fabric application and after running the Windows container on my machine a number of times I started getting the error : OutOfMemoryException , when compiling the Service Fabric portion of the build. 

At first I was a little confused as to what was going on, so like any developer... I googled it.

I found this: [Out of memory exception with windowsservercore container on Windows 10](https://sarafian.github.io/sdl/knowledge-center/2017/03/11/docker-windows-containers-out-of-memory-windows-10.html)

So understanding the default limitation of the containers being only 1Gig, sending additional memory to the build container seems to resolve the issue.

Here is a example of the command:

    docker run -m 2GB -v ${PWD}:c:\build -w c:\build --rm custom-build-image:some-tag powershell -c "Run-Awesome-Scritps"

Happy building...