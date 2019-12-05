---
title: Make gentoo-based docker image
date: 2019-12-05 22:52:15
tags:
categories:
- tooling
---

As a gentoo user, I want to build gentoo-based docker image to can deploy to other Linux distributions. Such a docker image is usually faster when compared to another one generated otherwise. This post documents my experience. In general, there are several steps as shown below.

1. Prepare systemd-based stage3 image (mine is stage3-amd64-systemd-20190323.tar.bz2)
2. make a chrooted environment, as we do in standard gentoo installation.
3. Install the package into an initially empty directory.
4. Make docker image from that installation directory.

Step 3 is the key. To do so, we need to use ROOT environment variable during emerge. Suppose that we want to make an image running nginx service. The command is as follows.

```
ROOT=/mnt/nginx-docker emerge bash shadow glibc nginx
```
(Tips: we can futher chroot to /mnt/nginx-docker to check if we can launch the service.)

The reason why emerging bash/shadow/glibc is that we use dynamic shared libraries when building nginx. Although I didn't try, I suspect that use ```emerge -e nginx``` would be OK in this case.

After emerge finishes, exit the chroot environment and run the following command:
```
cd /mnt/gentoo/mnt/nginx-docker
tar --numeric-owner -cj --to-stdout . |docker import - gentoo-nginx:base
```
What follows is standard docker image making.

Although it is fun to do so, I would not recommend other people to make gentoo-based docker images because there are many dark corners out there.
