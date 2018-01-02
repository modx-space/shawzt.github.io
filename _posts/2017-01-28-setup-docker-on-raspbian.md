---
layout: post
title:  Setup Docker On Raspbian
description: how to setup & configure docker on raspbian
modified: 2017-01-29
tags: Raspberry Docker
---

Originally, we need to install and configure lots of things on a host, no matter it is a traditional host or a VM, when we want to deploy the software or release a service. It is possible and not bad to do that only when we manage less than 5 hosts.

Personally, I hate and refuse to do that manually even when I play with the Raspberry Pi. Ex, here is the [Ansible Playbook][1] that I built to configure the Raspberry Pi in order to deploy [Pi-Dashboard][2] used to monitor the service running on Raspberry Pi.

> [DRY][3] `automate the things and save your life!`

And now, the container is much more popular, it makes the software development, deployment more agile and standardized. You may know Docker -- __Docker is the world's leading software containerization platform, and it is committed to build, ship and run software anywhere__. More about Docker, please read here [What is Docker][6].
![what-is-docker](/assets/images/docker-intro.png)

---

### So how to setup and configure Docker on Raspberry?

+ #### Install OS
  + If you haven't make the Pi to run, please refer to [how to active your Raspberry Pi][4]

+ #### Update System
```
sudo apt-get update
sudo apt-get dist-upgrade
sudo reboot
```

+ #### Install Docker
  + Due to the OS [Raspbian][5] is not in the Docker supported list, but there is a script that we can use:

  ```
  curl -sSL https://get.docker.com | sh
  ```
  ![docker-on-raspbian](/assets/images/docker-on-raspberry.png)

+ #### Verification
```
sudo docker version
ps aux | grep docker
```
![docker-info](/assets/images/docker-info.png)

---

### Play With Docker
Raspberry is based on ARMv7 architecture, and normal docker images are built with amd64. Please pull and run with the proper ARMv7 images here: [ARMv7 Images][7].

+ #### Check your system information

```
uname -a

# mine is here:
# pi@raspberrypi:~ $ uname -a
# Linux raspberrypi 4.4.38-v7+ #938 SMP Thu Dec 15 15:22:21 GMT 2016 armv7l GNU/Linux
```

+ #### Hello World

```
sudo docker run armhf/hello-world
```
![hello-world-by-docker](/assets/images/hello-world-by-docker.png)

[1]: https://github.com/shawzt/ansible-pi
[2]: https://github.com/shawzt/pi_dashboard
[3]: https://en.wikipedia.org/wiki/Don't_repeat_yourself
[4]: /posts/setup-raspberry
[5]: https://www.raspberrypi.org/downloads/raspbian/
[6]: https://www.docker.com/what-docker
[7]: https://hub.docker.com/u/armhf/
