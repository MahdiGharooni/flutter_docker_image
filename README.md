# Flutter Docker Image

[dockerHub](https://hub.docker.com/repository/docker/mahdigharooni/flutter)


[medium](https://medium.com/@mmahdi.gjafari69/setup-flutter-by-docker-without-using-ide-aed6c8b0db70)


If you want to use Flutter commands in server side, because of some server limitations you can add Flutter by docker instead of adding it by downloading Flutter SDK and other dependencies. By running some commands you can use Flutter SDK, Dart SDK and every dependencies you need more comfortable and professional.

Before this you should have basic information about [docker](https://docs.docker.com/) and download it. At least you should know about docker, dockerFile and docker-compose to understand easily.  

## Requirements

1- Add a file to your project Root, name it **DockerFile** .  It has these codes : 

```
FROM ubuntu:18.04

ENV ANDROID_HOME="/opt/android-sdk" \
    PATH="/opt/android-sdk/tools/bin:/opt/flutter/bin:/opt/flutter/bin/cache/dart-sdk/bin:$PATH"

RUN apt-get update > /dev/null \
    && apt-get -y install --no-install-recommends curl git lib32stdc++6 openjdk-8-jdk-headless unzip > /dev/null \
    && apt-get --purge autoremove > /dev/null \
    && apt-get autoclean > /dev/null \
    && rm -rf /var/lib/apt/lists/*

RUN git clone -b dev https://github.com/flutter/flutter.git /opt/flutter \
    && cd /opt/flutter \
    && git reset --hard $(git rev-list -1 $(git describe --tags @{u}))

RUN curl -s -O https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip \
    && mkdir /opt/android-sdk \
    && unzip sdk-tools-linux-4333796.zip -d /opt/android-sdk > /dev/null \
    && rm sdk-tools-linux-4333796.zip

RUN mkdir ~/.android \
    && echo 'count=0' > ~/.android/repositories.cfg \
    && yes | sdkmanager --licenses > /dev/null \
    && sdkmanager "tools" "build-tools;29.0.0" "platforms;android-29" "platform-tools" > /dev/null \
    && yes | sdkmanager --licenses > /dev/null \
    && flutter doctor -v \
    && chown -R root:root /opt

RUN ["apt-get", "update"]

RUN ["apt-get", "install", "-y", "vim"]

RUN ["apt-get", "install", "psmisc"]

WORKDIR /home/project

CMD tail -f /dev/null

```

after  run this file by docker, you have all dependencies you want. You can edit and update this file as your project. 


 2 - Add a .yml file to project root, name it **docker-compose.yml** . It has these codes: 

```
version: '3'
services:

  myapp:
    build:
      context: .
      dockerfile: ./Dockerfile
    container_name: myapp
    volumes:
      - ./:/home/project
```


this is the main file that docker will run  later. It'll create a docker container **myapp**, and you can use it to run flutter commands. 


## Usage

On server side, in project root you can use this command to build **myapp** container:

```
docker-compose up --build
```

if you create the container successfully, you can use it by : 

```
docker exec -it myapp bash
```

congratulation,  use flutter commands and enjoy Flutter . 

For having an update flutter SDK, you can use this command:

```
flutter upgrade
```

