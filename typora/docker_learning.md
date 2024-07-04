## ***docker***

- > windows desktop version

- install directly from official website

- or run ` winget install Docker.DockerDesktop ` on powershell

- in this way your docker'root is in windows but not wsl-ubuntu, all your commands should be executed on a windows terminal, for example, ` sudo systemctl restart docker ` on linus should be `Restart-Service docker` on windows or you can just restart it on docker desktop' s UI

- mirror gas pedal

  - docker desktop -> settings ->  Use the wsl2 based engine

  - -> resources -> change dick image location

  - -> docker engine ->

  - ```json
    {
        "builder": {
          "gc": {
            "defaultKeepStorage": "20GB",
            "enabled": true
          }
        },
        "experimental": true,
        "registry-mirrors": [
          "https://hub-mirror.c.163.com",
          "https://mirror.baidubce.com",
          "https://dzoprv55.mirror.aliyuncs.com"
        ]
    }
    ```

  - -> APPLY & RESTART(lower right)

- get image

  - ```bash
    $ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签] 
    ```

  - such as `$ docker pull ubuntu:22.04`

  - for more `$ docker pull --help`

- run

  - ```bash
    docker run -it --rm ubuntu:22:04 bash # ``--rm`` here means remove docker after exit
    		  
    docker run ubuntu:18.04 /bin/echo 'Hello world'
    		  
    docker run -t -i ubuntu:18.04 /bin/bash
    ```

  - `-i`: interactive

  - `-t`: open terminal (pseudo-tty)

  - ```bash
    $ docker container start # start exited docker
    		  
    ps | top # list pocesses
    ```

  - `-d`:guarded state operation

  - without `-d`:

    - ```bash
      $ docker run ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
      hello world
      hello world
      hello world
      hello world
      ```

  - with `-d`:

    - ```bash
      $ docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
      			  77b2dc01fe0f3f1265df143181e7b9af5e05279a884f4776ee75350ea9d8017a
      ```

  - to see docker info: `docker container ls`

  - to see output of docker: `docker container log`

  - to go into containers that was running background (start with `-d` parameters)

    - `docker attach` or `docker exec`(recommened)

    - `docker attach` - example:

      - ```bash
        docker run -d ubuntu
        				  
        docker contianer ls
        				  
        docker attach xxx
        root@xxx:/#
        ```

      - notice: if you exit from this terminal, it could cause the stop of docker container

    - `docker exec` -example:

      - ```bash
        docker run -d ubuntu
        				  
        docker container ls
        				  
        docker exec -i xxx bash # start bash shell in interative mode
        				  
        docker exec -it xxx bash
        ```

      - if you exit from this bash, it could not cause the stop of docker container, this is why we recommend `docker exec`.

- stop container

  - ```bash
    docker container stop # stop a runing container
    		  
    exit # stop the terminal
    		  
    docker container ls -a # view containers stopped
    		  
    docker container restart # stop a container, then restart it.
    ```

- export docker image && import

  - ```bash
    docker container ls -a
    		  
    docker export [CONTAINER ID] > xxx.tar
    ```

  - ```bash
    cat ubuntu.tar | docker import - test/ubuntu:v1.0
    		  
    docker image ls
    		  
    docker import http://example.com/exampleimage.tgz example/imagerepo # import container from certain URL or dictonary
    ```

- delete containers

  - ```bash
    docker container rm xxx
    		  
    docker container prune
    ```

- list docker images

  - ```
    docker image ls
    		  
    dcoker system df
    		  
    docker image ls -f dangling=true  # should dangling images
    		  
    docker iamge prune # delete dangling image
    		  
    # `` docker image ls `` only shows top images, if you want to see all images including middle images, run
    docker iamge ls -a
    		  
    docker iamge ls ubuntu
    		  
    docker image ls ubuntu:22.04
    		  
    docker image ls -f since=mongo:3.2 # filter since/before
    		  
    docker image ls -f label=com.example.version=0.1
    		  
    docker iamge ls -q # if you want to delete some iamges
    		  
    docker image ls --format "{{.ID}}: {{.Repository}}"
    		  
    docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
    ```

- docker delete

  - ```bash
    		  docker image ls
      		  
    docker image rm xxx #generally speaking, only the first three characters of IMAGE ID is enough,such as ``docker image rm 501``
    		  
    docker image rm centos
    		  
    docker image ls --digest
    docker image rm node@xxx:xxx #xxx:xxx is the digest of image
    		  
    docker image rm $(docker image ls -q [repo's name])
    docker image rm $(docker image ls -q -f before=mongo:3.2)
    ```

- `docker search` && `docker pull`

- `docker push`

  - ```bash
    docker tag REPOSITORY:TAG docker-hub-username/REPOSITORY:TAG
    		  
    docker image ls
    		  
    docker push docker-hub-username/REPOSITORY:TAG
    		  
    docker search docker-hub-username
    ```

- customize images

  - ``git commit``  is not recommended as it can contain a lot of thing that are irrelevant and useless for your image. what's worse, the process you construct the image is invisible for everyone else. the image can go heavier over time. because the result of any modification on image is simply to mark, add, or modify the current layer without altering the previous layer.

  - [x] we recommend to use **Dockerfile**, as a bash, we write every commends needed to construct the image on this file, so that it is visible for everyone to see the construction of the image. thus we can Optimize the construction of images to achieve light-weighting

    - 1. make  a new **empty** dictionary, and name it **Dockerfile**
         ```bash
         mkdir [project]
         cd [project]
         touch Dockerfile
         ```

         and in Dockerfile:

      2. ``FROM basic-image`` **important and must be placed at the beginning of file**
         and particularly, ``FROM scratch`` : scratch is an empty(only with some matedata in it) virtual image, in this image, you first ``RUN`` is the first layer of the image.

      3. ``RUN xxx``:
         when you execute a ``RUN``, you construct a new layer of image.
         so the number of ``RUN`` is the fewer the better. for example:

         - use
           ```bash
           FROM debian:stretch
           
           RUN set -x; buildDeps='gcc libc6-dev make wget' \
               && apt-get update \
               && apt-get install -y $buildDeps \
               && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
               && mkdir -p /usr/src/redis \
               && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
               && make -C /usr/src/redis \
               && make -C /usr/src/redis install \
               && rm -rf /var/lib/apt/lists/* \
               && rm redis.tar.gz \
               && rm -r /usr/src/redis \
               && apt-get purge -y --auto-remove $buildDeps
           ```

         - instead of:
           ```bash
           FROM debian:stretch
           
           RUN apt-get update
           RUN apt-get install -y gcc libc6-dev make wget
           RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
           RUN mkdir -p /usr/src/redis
           RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
           RUN make -C /usr/src/redis
           RUN make -C /usr/src/redis install
           ```

         as you can see,  

         - [x] it is without necessity to build a lot of layers.
         - [x] Getting into the habit of line breaks for different commands
         - [x] You can also see that at the end of this set of commands the command to clean up your work has been added, removing the software that was needed in order to compile the build, cleaning up all the files that were downloaded and expanded, and also cleaning up the apt cache files. This is a very important step, as we said before, images are stored in multiple tiers, and what is in each tier does not get deleted in the next tier, it stays with the image. So when the image is built, it is important to make sure that each tier only adds what really needs to be added, and anything extraneous should be cleaned up.

      4. In the directory where the **Dockerfile** file is located, execute the:
         ```bash
         docker build -t TAGname .  # -t here maens tag
         ```

      5. the ``.`` at the end of ``docker build`` commend means current dictionary. it aims to Specify Context. after that, some of you commends only go into effect within the context, such as ``COPY``:
         ``COPY ../package.json /app`` and ``COPY /opt/xxxx /app`` can't work.
         for more, see [[使用 Dockerfile 定制镜像 | Docker — 从入门到实践 (gitbook.io)](https://yeasy.gitbook.io/docker_practice/image/build)]

