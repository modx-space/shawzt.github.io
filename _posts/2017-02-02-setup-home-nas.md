---
layout: post
title:  Setup NAS For Home
description: how to setup storage service with RPi and Docker
modified: 2017-02-02
tags: raspberry docker nas
---

Normally when we want to backup data or transfer that among different devices, we need to do these actions one by one:

+ plug in the external drive to device-1  
+ transfer data from device-1 to the drive  
+ plug out the external drive from device-1  
+ plug in the external drive to device-#  
+ transfer data from the drive to device-#

The above described solution is not bad for common usage, but personally I like __DRY__ and make it in practise, which means I always try to automate the things that can be automated to save life. 😎

---

So here is the guide that I would like to share about __how to setup NAS for home with RPi and Docker__.

#### Preparation
+ [setup RPi][1]
+ [setup Docker on RPi][2]
+ [mount external HD on RPi][3]

#### Setup NAS
> I will use [Minio][4] and [Docker][5], here is the [docker image for minio-pi][6] built by myself

+ Create local persistent folder & config files
  - they will be mapped to docker container

  ```
  cd /mnt
  mkdir minio_storage
  cd /mnt/minio_storage
  # new and add the following configurations to file `config.json`
  # please update `credential` by yourself!
  # about onthers, mare sure you know how it works before updating.
  {
    "version": "13",
    "credential": {
      "accessKey": "minio-service",
      "secretKey": "shawzt2017"
    },
    "region": "home-internal",
    "logger": {
      "console": {
        "enable": true,
        "level": "error"
      },
      "file": {
        "enable": false,
        "fileName": "",
        "level": ""
      }
    },
    "notify": {
      "amqp": {
        "1": {
          "enable": false,
          "url": "",
          "exchange": "",
          "routingKey": "",
          "exchangeType": "",
          "mandatory": false,
          "immediate": false,
          "durable": false,
          "internal": false,
          "noWait": false,
          "autoDeleted": false
        }
      },
      "nats": {
        "1": {
          "enable": false,
          "address": "",
          "subject": "",
          "username": "",
          "password": "",
          "token": "",
          "secure": false,
          "pingInterval": 0,
          "streaming": {
            "enable": false,
            "clusterID": "",
            "clientID": "",
            "async": false,
            "maxPubAcksInflight": 0
          }
        }
      },
      "elasticsearch": {
        "1": {
          "enable": false,
          "url": "",
          "index": ""
        }
      },
      "redis": {
        "1": {
          "enable": false,
          "address": "",
          "password": "",
          "key": ""
        }
      },
      "postgresql": {
        "1": {
          "enable": false,
          "connectionString": "",
          "table": "",
          "host": "",
          "port": "",
          "user": "",
          "password": "",
          "database": ""
        }
      },
      "kafka": {
        "1": {
          "enable": false,
          "brokers": null,
          "topic": ""
        }
      },
      "webhook": {
        "1": {
          "enable": false,
          "endpoint": ""
        }
      }
    }
  }
  ```

+ Pull and run docker image `shawzt/minio-pi` as a daemon service

  ```
  docker run -d -p 9000:9000 --name minio_service \
  -v /mnt/minio_storage:/export \
  -v /mnt/minio_storage:/root/.minio \
  shawzt/minio-pi server /export
  ```
  ![Docker-Minio-Pi](/assets/images/docker-minio-pi.gif)

+ Login
  - visit `Your-RPi-IP:9000`
  - ![Minio-Login](/assets/images/minio-login.png)
  - ![Minio-Main](/assets/images/minio-main.png)

__Enjoy Your Private Cloud Storage Service Hosted By RPi & Docker & Minio 😀__

[1]: /posts/setup-raspberry
[2]: /posts/setup-docker-on-raspbian
[3]: /posts/mount-external-hd-on-raspbian
[4]: https://www.minio.io/
[5]: https://www.docker.com/what-docker
[6]: https://hub.docker.com/r/shawzt/minio-pi/
