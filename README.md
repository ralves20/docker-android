<p align="center">
  <img id="header" src="./images/logo_dockerandroid_small.png" />
</p>

[![Analytics](https://ga-beacon.appspot.com/UA-133466903-1/github/budtmo/docker-android/README.md)](https://github.com/igrigorik/ga-beacon "Analytics")
[![Join the chat at https://gitter.im/budtmo/docker-android](https://badges.gitter.im/budtmo/docker-android.svg)](https://gitter.im/budtmo/docker-android?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/budtmo/docker-android.svg?branch=master)](https://travis-ci.org/budtmo/docker-android)
[![codecov](https://codecov.io/gh/budtmo/docker-android/branch/master/graph/badge.svg)](https://codecov.io/gh/budtmo/docker-android)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/3f000ffb97db45a59161814e1434c429)](https://www.codacy.com/app/butomo1989/docker-detox?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=butomo1989/docker-detox&amp;utm_campaign=Badge_Grade)
[![GitHub release](https://img.shields.io/github/release/budtmo/docker-android.svg)](https://github.com/budtmo/docker-android/releases)
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fbudtmo%2Fdocker-android.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fbudtmo%2Fdocker-android?ref=badge_shield)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

Docker-Android-Detox is a docker image based on the Docker-Android https://github.com/budtmo/docker-android .

However this image includes the configuration of the tool wix/Detox https://github.com/wix/Detox to allow the to test react-native apps on Android devices using [detox]

For now there are just one Docker image for just one device which is: Nexus 5 - API 23 - Android 6.0.1

Emulator - Nexus Device      |
:---------------------------:|
![][emulator nexus]          |

Purposes
--------

1. Run UI tests for React-native apps with [detox]

Advantages compare with other docker-android projects
-----------------------------------------------------

1. noVNC to see what happen inside docker container
2. Ability to test react-native apps
3. First docker image that allows to run [detox] react-native tests
4. Ability to connect to Selenium Grid
5. Ability to control emulator from outside container by using adb connect
6. Support real devices with screen mirroring
7. Ability to record video during test execution for debugging
8. Integrated with other cloud solutions, e.g. [Genymotion Cloud](https://www.genymotion.com/cloud/)
9. Open source with more features coming

List of Docker images
---------------------

|OS   |Android   |API   |Browser   |Browser version   |Chromedriver   |Image   |Device name|
|:---|:---|:---|:---|:---|:---|:---|:---|
|Linux|6.0.1|23|browser|44.0|2.18|detox12-reactnative-nexus5-android6-api-23|nexus_5_6.0.1|

List of Devices
---------------

Type   | Device Name
-----  | -----
Phone  | Nexus 5

Requirements
------------

Docker is installed in your system.

Quick Start
-----------

1. Run Docker-Android

		```bash
		docker run --privileged -d -p 6080:6080 -p 5554:5554 -p 5555:5555 -e --name android-container ralves20/detox12-reactnative-nexus5-android6-api-23
		```

2. Verify the ip address of docker host.

   - For OSX, you can find out by using following command:

     ```bash
     docker-machine ip default
     ```

   - For different OS, localhost should work.

3. Open ***http://docker-host-ip-address:6080*** from web browser.


Running in your project with docker-compose
-----------

Pre-requirements:
   - You need to have docker-compose configurated on your computer.


1. There is a docker-compose as an example on example/sample-compose-android.yml, you can use it as a basis for your usage.


2. You are going to need to create an supervisord.conf file and using it as a volume for the supervisord inside the container, this is necessary because you need to modify supervisord section detox to run your detox tests. There is also an supervisord.conf example in example/sample-supervisord.conf.


3. Modify your detox-test section on the bash command to execute the command that you need to run your tests.

Example:
```
[program:detox-test]
command=bash -c "yes | sdkmanager --licenses && cd /tmp/test && yarn detox:start & cd /tmp/test && yarn detox:build-android && cd /tmp/teste && yarn detox:test-android"
autorestart=false
stdout_logfile=/tmp/detox/detox.log
stderr_logfile=/tmp/detox/detox_err.log
priority=5
```

4. Now in your project you need to modify your package.json in the detox section and set the device name as the name of the device you choose before.

Example:
```
      "android.emu.release": {
        "binaryPath": "android/app/build/outputs/apk/release/app-release.apk",
        "build": "cd android && RN_SRC_EXT=e2e.js E2E=true ./gradlew assembleRelease assembleAndroidTest -DtestBuildType=release && cd ..",
        "type": "android.emulator",
        "name": "nexus_5_6.0.1" //Here is the place where you need to set the device name
      }
```

4. After generated the docker-compose file and generating your supervisord.conf, you can run your detox tests.


Custom configurations
---------------------

[This document](README_CUSTOM_CONFIG.md) contains custom configurations of Docker-Android that you might need, e.g. Proxy, Changing language on fly, etc. 

Build Android project
---------------------

Docker-Android can be used for building Android project and executing its unit test. This following steps will illustrate how to build Android project:

1. Clone [this sample test project](https://github.com/googlesamples/android-testing).

    ```bash
    git clone git@github.com:googlesamples/android-testing.git
    ```

2. Build the project

    ```bash
    docker run -it --rm -v $PWD/android-testing/ui/espresso/BasicSample:/root/tmp budtmo/docker-android-x86-8.1 tmp/gradlew build
    ```
    
Control Android connected to host (Emulator or Real Device)
-----------------------------------------------------------
1. Create a docker container with this command 

	```
	$ docker run --privileged -d -p 6080:6080 -p 5554:5554 -p 5555:5555 -p 4723:4723 --name android-container-detox budtmo/docker-android-real-device
	```
	
2. Open noVNC [http://localhost:6080](http://localhost:6080)

3. Open terminal by clicking right on **noVNC** window >> **Terminal emulator**

4. To connect to host's adb (make sure your host have adb and connected to the device.) 

	```
	$ adb -H host.docker.internal devices
	```
	
	To specify port, just add `-P port_number`

	```
	$ adb -H host.docker.internal -P 5037 devices
	```
	
5. Now your container can access your host devices. But, you need to add `remoteAdbHost` and `adbPort` desired capabilities to make **detox** can recognise those devices.  


Control android emulator outside container
------------------------------------------

```bash
adb connect <docker-machine-ip-address>:5555
```

![][adb_connection]

**Note:** You need to have Android Debug Bridge (adb) installed in your host machine.

SMS Simulation
--------------

1. Using telnet
	- Find the auth_token and copy it.

	 ```bash
	 docker exec -it android-container cat /root/.emulator_console_auth_token
	 ```

	- Access emulator using telnet and login with auth_token

	 ```bash
	 telnet <docker-machine-ip-address> 5554
	 ```

	- Login with given auth_token from 1.step

	 ```bash
	 auth <auth_token>
	 ```

	- Send the sms

	 ```bash
	 sms send <phone_number> <message>
	 ```

2. Using adb

	 ```bash
	 docker exec -it android-container adb emu sms send <phone_number> <message>
	 ```

3. You can also integrate it inside project using adb library.

![][sms]

Google Play Services and Google Play Store
------------------------------------------

Docker-Android contains Google Play Service (v12.8.74) and Google Play Store (v11.0.50). Both applications are downloaded from [apklinker](https://www.apklinker.com/), so please be aware of it in case you use private/company account to that applications.

Jenkins
-------

This [document](README_JENKINS.md) gives you information about custom plugin that supports Docker-Android.

VMWARE
------

This [document](README_VMWARE.md) shows you how to configure Virtual Machine on VMWARE to be able to run Docker-Android.

Cloud
-----

This [document](README_CLOUD.md) contains information about deploying Docker-Android on cloud services.

Genymotion
----------

<p align="center">
  <img id="geny" src="./images/logo_genymotion_and_dockerandroid.png" />
</p>

For you who do not have ressources to maintain the simulator or to buy machines or need different device profiles, you need to give a try to [Genymotion Cloud](https://www.genymotion.com/cloud/). Docker-Android is integrated with Genymotion on different cloud services, e.g. Genymotion Cloud, AWS, GCP, Alibaba Cloud. Please follow [this document](README_GENYMOTION.md) or [this blog](https://medium.com/genymobile/run-your-detox-tests-using-docker-android-genymotion-cloud-e4817132ccd8) for more detail.

Troubleshooting
---------------
All logs inside container are stored under folder **/var/log/supervisor**. you can print out log file by using **docker exec**. Example:

```bash
docker exec -it android-container tail -f /var/log/supervisor/docker-android.stdout.log
```

Emulator Skins
--------------
The Emulator skins are taken from [Android Studio IDE](https://developer.android.com/studio) and [Samsung Developer Website](e)

Security
--------
All docker images are protected by [Polyverse](https://polyverse.io/) by scrambling the Linux packages. For more information please read [this](https://polyverse.io/how-it-works/)

Special Thanks
--------------
- [Gian Christanto] for creating a great logo!
- [Otoniel Isidoro] for giving some ideas on how to adapt the code to use Detox

LICENSE
--------------
See [License](LICENSE.md)

[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fbudtmo%2Fdocker-android.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Fbudtmo%2Fdocker-android?ref=badge_large)

[detox]: <https://github.com/wix/Detox>
[espresso]: <https://developer.android.com/training/testing/espresso/>
[robotium]: <https://github.com/RobotiumTech/robotium>
[emulator samsung]: <images/emulator_samsung_galaxy_s6.png>
[emulator nexus]: <images/emulator_nexus_5.png>
[real device]: <images/real_device.png>
[adb_connection]: <images/adb_connection.png>
[sms]: <images/SMS.png>
[gian christanto]: <https://www.linkedin.com/in/gian-christanto-0b398b131/>
[otoniel isidoro]: <https://www.linkedin.com/in/otoniel-isidoro-61b38512/>