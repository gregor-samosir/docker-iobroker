# ioBroker for Docker

[![Release](https://img.shields.io/github/v/release/buanet/docker-iobroker)](https://github.com/buanet/docker-iobroker/releases)
[![Build Status](https://travis-ci.org/buanet/docker-iobroker.svg?branch=master)](https://travis-ci.org/buanet/docker-iobroker)<br>
[![Image Size](https://img.shields.io/docker/image-size/buanet/iobroker)](https://hub.docker.com/repository/docker/buanet/iobroker)
[![Docker Pulls](https://img.shields.io/docker/pulls/buanet/iobroker)](https://hub.docker.com/repository/docker/buanet/iobroker)
[![Docker Stars](https://img.shields.io/docker/stars/buanet/iobroker)](https://hub.docker.com/repository/docker/buanet/iobroker)<br>
[![Source](https://img.shields.io/badge/source-github-blue)](https://github.com/buanet/docker-iobroker)
[![License](https://img.shields.io/github/license/buanet/docker-iobroker)](https://github.com/buanet/docker-iobroker/blob/master/LICENSE.md)
[![Donate](https://img.shields.io/badge/donate-paypal-blue)](https://paypal.me/buanet)

IoBroker for Docker is a ready to use Docker image for ioBroker IoT platform (http://www.iobroker.net).

It was originally created for a Synology Disk Station 1515+ with DSM 6 and official Docker package installed but should run on any other Linux based Docker host too.

After more than three years of development it might be the best documented and still maintained Docker image for running ioBroker IoT platform with Docker.

Since v4.0.0 the image is available for the following architectures: amd64, armv7hf, aarch64.<br>
If you need more please let me know by opening a Github issue.

## Important notice

In general a new major version (e.g. v2, v4, v5) of the image comes with a new, preinstalled major node version!
If you are updating an existing installation to a new major version (e.g. from v4 to v5) you have to perform some additional steps inside ioBroker! For more details please see official ioBroker documentation: [EN](https://www.iobroker.net/#en/documentation/install/updatenode.md) | [DE](https://www.iobroker.net/#de/documentation/install/updatenode.md).<br>

You might avoid these procedure if you use my "Best practice" hint for "upgrading your ioBroker container".

In any case make a backup first!

## Getting started

A detailed tutorial (German, based on v3.0.0) can be found here: [https://buanet.de](https://buanet.de/2019/05/iobroker-unter-docker-auf-der-synology-diskstation-v3/). Please notice that the old tutorial is outdated and does no longer work!

For discussion and support please visit [ioBroker forum thread](http://forum.iobroker.net/viewtopic.php?f=17&t=5089) or use the comments section at the linked tutorial.

Please do not contact me directly for any support-reasons. Every support question should be answered in a public place so every user can benefit from it . Thanks in advance.

If you think you found a bug or simply want to request a new feature please open an issue on Github so we can talk about.

The following ways to get iobroker-container running are only examples. Maybe you have to change, add or replace parameters to configure ioBroker for fitting your needs.

### Running from command line

For taking a first look at the iobroker docker container it would be enough to simply run the following basic docker run command:

```
docker run -p 8081:8081 --name iobroker -v iobrokerdata:/opt/iobroker buanet/iobroker:latest
```

### Running with docker-compose

You can also run iobroker by using docker-compose. Here is an example:

```
version: '2'

services:
  iobroker:
    restart: always
    image: buanet/iobroker:latest
    container_name: iobroker
    hostname: iobroker
    ports:
      - "8081:8081"
    volumes:
      - iobrokerdata:/opt/iobroker
```

## Special settings and features

The following will give a short overview.

### Environment variables

To configure the ioBroker container on startup it is possible to set some environment variables.
You do not have to declare every single variable when setting up your container. Variables you do not set will come up with their default value.

**Important: In v4.2.0 the ENVs "ADMINPORT" and "REDIS" were renamed/ reorganized. For Details see the following table!**

|ENV|Default|Description|
|---|---|---|
|AVAHI|false|Installs and activates avahi-daemon for supporting yahka-adapter, can be "true" or "false"|
|IOB_ADMINPORT|8081|Sets ioBroker adminport on startup|
|IOB_MULTIHOST|[not set]|Sets ioBroker instance as "master" or "slave" for multihost support (needs additional config for objectsdb and statesdb!)|
|IOB_OBJECTSDB_HOST|127.0.0.1|Sets host for ioBroker objects db|
|IOB_OBJECTSDB_PORT|9001|Sets port for ioBroker objects db|
|IOB_OBJECTSDB_TYPE|file|Sets type of ioBroker objects db, cloud be "file", "redis" or "couch"|
|IOB_STATESDB_HOST|127.0.0.1|Sets host for ioBroker states db|
|IOB_STATESDB_PORT|9000|Sets port for ioBroker states db|
|IOB_STATESDB_TYPE|file|Sets type of ioBroker states db, could be "file" or "redis"|
|LANG|de_DE.UTF&#x2011;8|The following locales are pre-generated: de_DE.UTF-8, en_US.UTF-8|
|LANGUAGE|de_DE:de|The following locales are pre-generated: de_DE:de, en_US:en|
|LC_ALL|de_DE.UTF-8|The following locales are pre-generated: de_DE.UTF-8, en_US.UTF-8|
|PACKAGES|[not set]|Installs additional linux packages to your container, packages should be seperated by whitespace like this: "package1 package2 package3"|
|SETGID|1000|For some reasons it might be useful to specify the gid of the containers iobroker user to match an existing group on the docker host|
|SETUID|1000|For some reasons it might be useful to specify the uid of the containers iobroker user to match an existing user on the docker host|
|TZ|Europe/Berlin|All valid Linux-timezones|
|USBDEVICES|none|Sets relevant permissions on mounted devices like "/dev/ttyACM0", for more than one device separate with ";" like this: "/dev/ttyACM0;/dev/ttyACM1"|
|ZWAVE|false|Will install openzwave to support zwave-adapter, can be "true" or "false"|

### Mounting folder/ volume

It is possible to mount an empty folder to /opt/iobroker during first startup of the container. The startup script will check this folder and restore content if it is empty.

Since v4.1.0 it is also possible mount a folder filled up with an iobroker backup file created using `iobroker backup` command or backitup adapter. The name of your backup file ending like this: `*_backupiobroker.tar.gz"`.

The startup script will then detect this backup and restore it during the start of the container. Please see container logs when starting the container for more details!

Note: It is absolutely recommended to use a mounted folder or persistent volume for /opt/iobroker folder!

You can also mount a folder containing an existing ioBroker-installation (e.g. when moving an existing installation to docker).
But watch for the used node version. If the existing installation runs with another major version of node you have do perform additional steps. For more Details see the "Important notice" on top of this readme.md file.

**Important: If the folder you mount to /opt/iobroker in your container is placed on a mounted device, partition or other storage, the mountpoint on your host should NOT have the "noexec" flag activated. Otherwise you may get problems executing ioBroker inside the container!**   

### Mounting USB device

If you want to use a USB device within ioBroker inside your container don´t forget to [mount the device](https://docs.docker.com/engine/reference/commandline/run/#add-host-device-to-container---device) on container startup and use the environment variable "USBDEVICES".

### User defined startup scripts

It is possible to add some script code to container startup with the help of the userscripts feature. You can get this to work by mounting an additional folder to `/opt/userscripts` into the container.

When you mount an empty folder the startup script will restore two example scripts in there. To activate the scripts you have to remove the `_example` part of the name. The "userscript_firststart.sh" will execute only at the very first start of a new container, while the "userscript_everystart.sh" will execute on every container start.

Feel free to test it with my example code. Take a look at the log. In "Step 4 of 5: Applying special settings" you will see the messages generated by the example userscripts.

### Multihost (beta)

With the help of the ENV "IOB_MULTIHOST" and the ENVs for objects and states db connections (see ENVs table above) it is now possible to run a container as standalone, multihost master or multihost slave. This is more or less a feature for advanced users. Please make sure you know how ioBroker multihost is working and set the ENVs as with `ìobroker setup custom`.

For general information about iobroker multihost feature please see [official ioBroker documentation](https://www.iobroker.net/docu/index-24.htm?page_id=3068&lang=de).

### Healthcheck (beta)

Since v5.0.2beta the image contains a simple Docker healthcheck. At the moment it only checks if js-controller is running inside the container and reports "healthy" or "unhealthy" to the Docker daemon. Further development is planned.

The healthcheck is configured to 5 retries in an 15s interval with a timeout of 5s. So a container needs a minimum of one minute to get unhealthy.   

Hint: As the Docker daemon itself gives no opportunity to automatically restart an unhealthy container you might want to setup some kind of "watchdog container" like this simple one: https://hub.docker.com/r/willfarrell/autoheal/ (works great so far!).

### Maintenance script (beta)

Within the implementation of the docker health check (above) some manual maintenance actions, like stopping ioBroker for upgrading js-controller, would cause the container to get "unhealthy" and may cause an external watchdog to automatically restart it.

In this case you can use the new maintenance command line tool inside the container. By simply typing `maintenance on` it will activate some kind of "maintenance mode" and automatically stop ioBroker while the container stays healthy.

After your maintenance is done just type `maintenance off`. Depending on the selected restart policy of your container, the command will stop (and automatically restart) it.

## Best practices

### Avoid using "latest" tag

To avoid conflicts when upgrading your container or getting in trouble when accidentally upgrading your container to a new major version I prefer using the version tag like "V4.2.0" instead of "latest" for creating your container.

### Upgrading your container

If you want to upgrade your ioBroker container to a new major version (e.g. from v4 to v5) I would prefer to do that by creating a backup in ioBroker (by "iobroker backup" or backitup adapter) and restoring it to a completely new container. All you need is time an the following steps:
* make a backup by command line ("iobroker backup") or backitup adapter
* stop the old container
* create a new and empty data folder or volume and place your backup file in it
* create a new container as your old or as you need it and use the new data folder/ volume for the /opt/iobroker mount point
* follow the log output of the container and be patient

After this the startup script inside the container will automatically detect and restore your backup to a new ioBroker instance. When iobroker is started after the restore it will install your adapters to the new ioBroker instance by itself. This might take some time but will get you the best and cleanest results...

## Miscellaneous

### Beta testing

If you want to get the newest features and changes feel free to use/ test the beta version of the Docker image. You can find the readme.md file for beta versions [here](https://github.com/buanet/docker-iobroker/blob/beta/README.md). Please make sure to read the changelog before testing beta versions.

### Subscribe to updates

If you want the newest updates about the image and my tutorials at https://buanet.de/tutorials you can simply subscribe to my new "news and updates" channel (only in German) on Telegram.
You will find the channel here: https://t.me/buanet_tutorials

### Support the project

The easiest way to support this project is to leave me some likes/ stars on Github and Docker hub!<br>
If you want to give something back, feel free to take a look into the [open issues](https://github.com/buanet/docker-iobroker/issues) or the [ioBroker forum thread](http://forum.iobroker.net/viewtopic.php?f=17&t=5089) and helping me answering questions, fixing bugs or adding new features!<br>
And if you want to buy me a beer instead, you can do this here: <a href="https://www.paypal.me/buanet" target="_blank"><img src="https://buanet.de/wp-content/uploads/2017/08/pp128.png" height="20" width="20"></a><br>
Thank you!

## Changelog

### v5.0.2beta (2020-07-28)
* extend readme.md doku
* added maintenance script
* added container healthcheck
* fixed configuration procedure and logging for objects and states db setup
* v5.0.1beta (2020-07-01)
  * fixing backup detection in startup script
  * fixing permission issue on iobroker restored
  * extended Logging
  * optimize multihost support

### v5.0.0 (2020-06-29)
* v4.2.4beta (2020-06-23)
  * added graceful shutdown
  * small fix for GID/UID handling
  * adding new ENV "IOB_MULTIHOST" for multihost support
  * small syntax fixes in iobroker_startup.sh
* v4.2.3beta (2020-06-05)
  * ~~updating js-controller to not stable version 3.1.5 to fix renaming issue~~ (is stable now)
* v4.2.2beta (2020-06-03)
  * ~~workaround for renaming issues on startup~~ (fixed in js-controller)
* v4.2.1beta (2020-05-10)
  * using node 12 instead of 10
  * updated documentation in readme.md

### v4.2.0 (2020-04-14)
* v4.1.4beta (2020-04-07)
  * switching base image to buster
  * optimizing installation of packages defined by ENV "PACKAGES"
* v4.1.3beta (2020-02-08)
  * renamed ENV for adminport (new "IOB_ADMINPORT)")
  * added new ENVs for "iobroker setup custom" (replacing "REDIS")
  * enhancements in startup script logging
* v4.1.2beta (2020-02-02)
  * added feature for running user defined scripts on startup
  * small fix for permissions issues on some systems
* v4.1.1beta (2020-01-17)
  * updated openzwave to version 1.6.1007

### v4.1.0 (2020-01-17)
* improved readme.md
* v4.0.3beta (2020-01-06)
  * added support to restore backup on startup
  * small fixes according to "docker best practices"
* v4.0.2beta (2019-12-10)
  * ~~added env for activating redis~~
  * enhancements in startup script and docker file
* v4.0.1beta (2019-11-25)
  * added env for iobroker admin port
  * added env for usb-devices (setting permissions)
  * updateing prerequisites for iobroker installation
  * some small code fixes

### v4.0.0 (2019-10-25)
* v3.1.4beta (2019-10-23)
  * added env for zwave support
* v3.1.3beta (2019-10-17)
  * enhanced logging of startup-script
  * multi arch support (amd64, aarch64, armv7hf)
* v3.1.2beta (2019-09-03)
  * using node 10 instead of node 8
* v3.1.1beta (2019-09-02)
  * adding env for setting uid/ gid for iobroker-user

### v3.1.0 (2019-08-21)
* v3.0.3beta (2019-08-21)
  * switching base image from "debian:latest" to "debian:stretch"
* v3.0.2beta (2019-06-13)
  * using gosu instead of sudo
  * changing output of ioBroker logging
* v3.0.1beta (2019-05-18)
  * ~~switching back to iobroker-daemon for startup~~

### v3.0.0 (2019-05-09)
* v2.0.6beta (2019-04-14)
  * added some additional logging
  * fixing some issues for language env
  * added permission fixing on first start
* v2.0.5beta (2019-02-09)
  * added ENV to docker file
  * added EXPOSE for admin
  * final testing
* v2.0.4beta (2019-01-28)
  * added support for env variables "avahi" and "packages"
  * moving avahi-daemon installation into avahi startup script
  * added script for installing optional packages
  * optimizing logging output
* v2.0.3beta (2019-01-24)
  * added support for running ioBroker under iobroker user
  * optimizing logging output
  * optimizing scripts
* v2.0.2beta (2019-01-23)
  * optimizing and rearranged docker file
  * changes for new ioBroker install script
  * added restoring for empty mounted /opt/iobroker folder
  * some more small fixes
* v2.0.1beta (2019-01-07)
  * some changes for supporting other docker-environments than synology ds

### v2.0.0 (2018-12-05)
* v1.2.2beta (2018-12-05)  
  * using node8 instead of node6
  * changes for new iobroker setup
* v1.2.1beta (2018-09-12)
  * added support for firetv-adapter

### v1.2.0 (2018-08-21)
* v1.1.3beta (2018-08-21)
  * ~~added ffmpeg-package for yahka to support webcams~~
* v1.1.2beta (2018-04-04)
  * added ENV for timezone issue
* v1.1.1beta (2018-03-29)
  * added wget package
  * updated readme.md

###  v1.1.0 (2017-12-10)
* v1.0.2beta (2017-12-10)
  * changed startup call to fix restart issue
  * fixed avahi startup issue
  * fixed hostname issue
  * added z-wave support
  * added logging to /opt/scripts/docker_iobroker_log.txt
* v1.0.1beta (2017-08-25)
  * fixed locales issue

### v1.0.0 (2017-08-22)
* moved and renamed iobroker startup script
* disabled iobroker daemon to (hopefully) fix restart issue
* added some maintenance scripts

### v0.2.1 (2017-08-16)
* ~~added libfontconfig package (for iobroker.phantomjs)~~
* added gnupg2 package as prerequisite for installing node version 6

### v0.2.0 (2017-06-04)
* fixed startup issue in startup.sh
* changed node version from 4 to 6

### v0.1.2 (2017-03-14)
* ~~added libpcap-dev package (for iobroker.amazon-dash)~~

### v0.1.1 (2017-03-10)
* added git package

### v0.1.0 (2017-03-08)
* moved avahi-start.sh to seperate directory
* fixed timezone issue (sets now timezone to Europe/Berlin)

### v0.0.2 (2017-03-06)
* added support for avahi-daemon (installation and autostart)

### v0.0.1 (2017-01-31)
* project started / initial release

## License

MIT License

Copyright (c) 2017 [André Germann]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Credits

Inspired by https://github.com/MehrCurry/docker-iobroker
