# Basics

Images are read-only templates, but you can diff and push them to save
many versions. Containers are instances of images, that are also
directories and usually a single running process.

Images can be layered, you start from a base and make
changes. Containers are versioned so that you can make changes and
save the filesystem state as a new Image.

You can poke around in a container before starting it running, and
after it's died.  You can attach or exec a shell into a container to
find out what's going on.

Network is also virtualized so that several containers can communicate
with each other, but this isn't visible outside unless configured to
be.


# Images

    # Dockerfile
    docker build -t $IMAGE
    docker images
    docker rmi $IMAGE
    
    # find pre-made images
    docker search $TERM


# Containers

    docker run --name $CONTAINER -it $IMAGE
    docker ps
    # see history
    docker ps -a
    docker stop $CONTAINER

    docker container ls -a
    docker rm $CONTAINER

# Debugging

    docker logs $CONTAINER
    docker attach $CONTAINER   # warning control-C kill
    docker exec -it $CONTAINER /bin/bash  # if running
    
    # quick little commands to inspect running container
    docker exec CONTAINER env
    docker exec CONTAINER ls
    docker exec CONTAINER ps fax
    docker exec CONTAINER ip addr show eth0

    # if not running, starts a container and shells in
    # note entrypoint before image!
    docker run -it --entrypoint /bin/bash $CONTAINER
    docker run -it $CONTAINER sh  # works with caveats

    # same but as root
    docker run -u root -it --entrypoint /bin/bash $IMAGE

    docker exec CONTAINER apt install net-tools  # for netstat
    docker exec CONTAINER netstat -natp
    apt install -y binutils  # shell stuff
    apt install -y busybox  # same but lighter
    # others: procps, ..
    
    # check on a container's port mapping
    docker port $CONTAINER

    # entrypoints often end with exec "$@" so they can run whatever, eg.
    docker run -it $CONTAINER python manage.py migrate
    docker run -it $CONTAINER python manage.py /bin/bash

    # low-level details for many docker objects
    # eg. volume mounts
    docker inspect $CONTAINER
    
    # general: hack up entrypoint.sh for debugging


# Iterating

    docker diff $CONTAINER
    docker commit $CONTAINER $IMAGE[:tag]
    docker images
    docker rmi $IMAGE
    
Jumping into the middle of a build to explore:

```
Building stress-grpc-component-tests
Step 1/7 : FROM docker.artifactory.com/dotnet/core/sdk:3.1
 ---> 006ded9ddf29
Step 2/7 : COPY tests/Component/*.csproj /all/tests/Component/
 ---> Using cache
 ---> e66c8b271d16
Step 3/7 : WORKDIR /all/tests/Component
 ---> Using cache
 ---> 4d4c013a1322
Step 4/7 : RUN dotnet restore --source https://artifactory.com/api/nuget/nuget-all
 ---> Using cache
 ---> 138c448d6f4a
Step 5/7 : COPY tests/Component /all/tests/Component
 ---> Using cache
 ---> 9674619ec356
Step 6/7 : RUN dotnet build --no-restore -c Debug
 ---> Using cache
 ---> 590e931b538f
Step 7/7 : ENTRYPOINT ["dotnet", "test", "--no-restore", "--no-build"]
 ---> Using cache
 ---> 787003ca8bca

Successfully built 787003ca8bca
Successfully tagged docker.artifactory.com/stress-test/stress-grpc-component-tests:latest

C:\src\stress-test (cgt-test-4)
$ docker run 138c448d6f4a -it
C:\ProgramData\chocolatey\lib\docker-cli\tools\docker.exe: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-it\": executable file not found in $PATH": unknown.

C:\src\stress-test (cgt-test-4)
$ docker run -it 138c448d6f4a
root@c3b817abd422:/all/tests/Component# ls /api
ls: cannot access '/api': No such file or directory
root@c3b817abd422:/all/tests/Component# cd /all/tests/Component/
root@c3b817abd422:/all/tests/Component# ls
Stress.Grpc.Component.Tests.csproj  obj
root@c3b817abd422:/all/tests/Component# ls obj
Stress.Grpc.Component.Tests.csproj.nuget.dgspec.json  Stress.Grpc.Component.Tests.csproj.nuget.g.targets  project.nuget.cache
Stress.Grpc.Component.Tests.csproj.nuget.g.props      project.assets.json
root@c3b817abd422:/all/tests/Component#
```

# Compose

    docker-compose build
    docker-compose create
    docker-compose start
    docker-compose ps
    docker-compose logs

    # or just
    docker-compose up
    docker-compose down


# General

- Order of arguments is very important!
- On dev if you have a docker-compose with a "volume" you don't need a
  full rebuild for changes to our code.
- Warning: things have changed in docker and compose, so old tutorials
  are sometimes a bit wrong.
- Permissions and users can be super confusing, beware.                                                               |- 
- Don't trust non-official pkgs, look closely

# Gotchas

- nginx: needs to run as root, then switches user on it's own, so be careful to use sudo in entrypoint.sh
- CMD only runs the *last* one and silently ignores earlier.  Also "CMD foo > out.bar" is "CMD ["sh", "-c", ..."


# Reference

- Docker:
  - Docker in 12 min video: https://www.youtube.com/watch?v=YFl2mCHdv24&list=FLhMiuJQ3n7PanRI62ibUkuQ&index=1&t=426s
  - Dockerfile commands: https://docs.docker.com/engine/reference/builder/
- Docker Compose:
  - Docker Compose in 12 min video: https://www.youtube.com/watch?v=Qw9zlE3t8Ko&t=271s
  - https://docs.docker.com/compose/overview/
  - https://docs.docker.com/compose/install/
- Debugging: https://medium.com/@betz.mark/ten-tips-for-debugging-docker-containers-cde4da841a1d
- Future: Shipping Python Applications in Docker: https://orbifold.xyz/python-docker.html

2020-08-12
- This one has some advanced stuff https://github.com/wsargent/docker-cheat-sheet
- Nice printable desktop reference http://dockerlabs.collabnix.com/docker/cheatsheet/
- More of an article / quick tutorial https://www.jrebel.com/blog/docker-commands-cheat-sheet
