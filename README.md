## Install Docker
  1. Download Docker CE.
  2. Install docker bash completion:  https://docs.docker.com/docker-for-mac/#installing-bash-completion

## Docker Commands
  1. **docker version** - Shows the version of docker CLI and docker engine. Run this command to check if docker is up and running.
  2. **docker info** - Information regarding the docker engine.
  3. **docker container commands** - 
     1. _docker container run --publish 80:80 nginx_ - Downloads the latest docker image and runs it. The publish option is to map traffic of local port 80 to docker container port 80. The process runs in foreground.
     2. _docker container run --publish(-p) 
80:80 --detach(-d) --name webhost -e <envprop=abcd> --network <network name> nginx_ - Runs the docker container in background by returning the container id. The container name will be webhost.Using -e option pass env variable. Using --network option specify the docker private network to which the container should belong.
     3. _docker container stop <container id>_ - Stops the running container.
     4. _docker container ls_ - Lists all the docker running containers.
     5. _docker container ls -a_ - Lists all the docker containers. 
     6. _docker container logs <docker id/name>_ : Gets the logs of the container.
     7. _docker container top <docker id/name>_ : Get the list of process run by the container
     8. _docker container --help_: Gets all the subcommands.
     9. _docker container rm -f <container id>_ :  Removes the docker container.
     10. _docker container stats_ : Gives the statistics of all the container running like CPU utilization.
     11. _docker container inspect_: Details of one container config.
     12. _docker container -it run nginx bash_: Runs the container in interactive mode. Opens a bash to nginx container.
     13. _docker container -it exec nginx bash_: Opens an interactive shell to running container.
     14. _docker container -ai start nginx_: run a container with image name nginx in interactive shell mode.

 4. **docker network commands**: Docker container resides in virtual private network. It communicates to the physical network using the port specified in publish option. Default docker network is bridge or docker0. It is better to group related containers into a private network.
    1. _docker container port container_name_: lists the container port within docker engine.
    2. _docker network ls_: lists all the private networks present in docker.
    3. _docker network create --driver driver_name_: Creates a private network for the given driver name.
    4. _docker network inspect network_name_: get the details of a network. It also lists all containers within that network.
    5. _docker network connect/disconnect network_name container_name>_: Connect/disxoonect a container to a network.
    6. docker DNS: docker assigns a default host name to each container which will be the container name. Hence we need to depend on the container IP for communication.
docker exec container -it my_nginx ping nginx2

5. **docker image** : All the official and other public images are available in https://hub.docker.com/.
    1. _Download image_: docker pull image_name : tag. If tag is not provided it will download default image.
    2. _docker image history image_name_ : Shows all layers of image. An image consist of multiple layers. Each layer has a unique sha key for identification and is stored only once in local cache. If we try to download another image which contains a layer already present in local cache, it will not be downloaded. For example two docker images might be referring to mysql layer for DB connectivity. When we run a container it creates a read-write layer over the image. If any customisation is done in container, a copy of file from image is created in the container.
    3. _docker image inspect image_name_ : Displays the details about the image like id, author, default env variable and command line args.
    4. _docker image tag existing_image_reponame:tag new_image_reponame:tag_ : Creates another tag for existing image. The image id remains same. The new image repo name should contain username/reponame. Only public repos don't have user name and are at root.
    5. _Pushing an image to docker hub_: 
          1. docker login : Prompts to enter docker username and password. It is then saved in machine's .profile.
          2. docker image push image_reponame:tag

6. **Docker File**: Ref: https://docs.docker.com/engine/reference/builder/
    - The Docker file should be named as "Dockerfile". It contains a set of commands. Each command creates layer in image. The resultant output is an image.
    - _docker build -f /Users/emathur/Documents/docker/sample1/Dockerfile -t eshita19/sample1 ._ : This command builds an image using the Dockerfile specified with -f option. The repo name and tag will be specified using -t option.
    - Docker File commands:
         1. _FROM imagename:tag AS customname_: The FROM instruction initializes a new build stage and sets the Base Image for subsequent instructions. 
         2. _ARG a=b_ : Initializes a variable which will be available only for image build but not during container run. We can use ARG using '${a}' in next instruction. In order to use it again in subsequent line define it as 'ARG a' again. Change the value dynamically thru command line by using 'docker build .... --build-arg a=c'.
         3. _ENV a=b_ : Initializes a variable which will be avilable duirng container runtime as well.
         4. _RUN mkdir -p /opt/emathur_ : Runs a command only during image build but not during container run.
         5. _CMD/ENTRYPOINT echo "Hi"_ : Runs a command only during container run. There can only be single command stmt in entire docker file. If multiple CMD are present last one will be considered.CMD instruction allows you to set a default command, which will be executed only when you run container without specifying a command. If Docker container runs with a command, the default command will be ignored.But ENTRYPOINT command and parameters are not ignored when Docker container runs with command line parameters. Entrypoint overrides and CMD defined.
         6. _LABEL a="b"_ : The LABEL instruction adds metadata to an image. A LABEL is a key-value pair. 
         7. _EXPOSE port [port/protocol...]_: The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime. To actually publish the port when running the container, use the -p flag.
         8. _ADD/COPY hom* /mydir/_: COPY takes in a src and destination. It only lets you copy in a local file or directory from your host (the machine building the Docker image) into the Docker image itself.
ADD lets you do that too, but it also supports 2 other sources. First, you can use a URL instead of a local file / directory. Secondly, you can extract a tar file from the source directly into the destination.
         9. _WORKDIR /usr/local_: Change directory to /usr/local. Always use this before COPY or ADD.
         20. _VOLUME mysql-db:/data_ : Creates a volume.

7. **Docker lifetime and persistent data**:
    - **Docker Volume** : Volumes are persisted even if the container is stopped.
        - docker container run -d --name mysql1 -v host-dir/volume-name:volume-dir-in-container mysql.
          - host-dir/Bind mount : The absolute path of directory in host system which is exact replica of volume.
          - volume name: If a absolute path is not present, provide name to volume.
          - volume-dir-in-container: Absolute path of volume inside container.
        - docker volume ls : lists all volumes
        - docker volume inspect mysql-db : Info regarding mysql-db named volume.
        - docker volume create: Creates a volume. we can specify the driver.
8. **Docker compose**:https://takacsmark.com/docker-compose-tutorial-beginners-by-example-basics/, https://docs.docker.com/compose/compose-file/
   - version : Version of docker compose to use. Mentioned in quotes. eg: version: "3.3"
   - build : Build is to specify the config used to build an image.
     - build : ./ (Build the image from current directory's docker file)
     - build:
       - context: ./ (Path of docker file)
       - dockerfile: Dockerfile (Docker file name)
       - args: (Arg passed to during docker image built)
         - arg1: val1
   - image: imagename:tag (The resultant image name and tag)
   - container_name: The name of the container which comes up.
   - CACHE_FROM: A list of images that the engine uses for cache resolution.
   - labels: Add metadata to the resulting image.
   - depends_on : Lists the images which should be build before this image.
   - ports: Specify the exposed ports.
   - networks: specify the network cluster container should be part of.
   - volumes: specify the volume and bind mounts.
   - entrypoint: override default entry point.
   - environment: add environment variables.  
                
        

      
     
  

    
     

 
