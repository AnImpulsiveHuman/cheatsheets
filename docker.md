# __Docker__
### Dockerfile commands
```
FROM <image name>                      -> defines the base image
COPY <source> <destination>            -> copies file from directory containing the Dockerfile to the 
                                          container's image
ADD <source> <destination>             -> similar to COPY but allows tar and remote URL handling
RUN <command>                          -> executes a command in the terminal
RUN ["command","arg1","value",..]      -> executes a command with	arguments
CMD <command>                          -> defines default command to executes when the container launches
CMD ["command","arg1","value",..]      -> defines default command with arguments to execute when the 
                                          container launches (will be ignored if container is run with a command)
ENTRYPOINT                             -> similar to CMD (wont be ignored if container is run with a command)
EXPOSE <port number>                   -> opens a specific port to the outside world
WORKDIR <directory>                    -> defines a working directory where all future commands will be executed
ONBUILD <Dockerfile command>           -> executes the specific Dockerfile command only if the current image is 
                                          used as a base image in other builds

NOTE: Use COPY whenever the additional features of ADD are not required
```
### build
```
docker build -t <image name> .   -> builds the image from Dockerfile with the specified image name
docker build <url>               -> builds the image from the url
```
### search
```
docker search <name>              -> search image

options:
.......
--no-trunc <name>                -> does not truncate description
-f <filter> <name>               -> filter the result (supported filters:stars, is-official, is-automated)
--limit <1-100> <name>           -> limits the number of results diplayed
--format {{<placeholder>}}       -> pretty prints output using a Go template

Placeholder:
.Name
.Description
.StarCount
.IsOfficial
.IsAutomated
```
### run
```
docker run <name>                           -> start container (foreground)

options:
.......
-d                                          -> start container in detached mode (background)
--privileged                                -> gives extended privileges to the command
-p <host port>:<container port>             -> bind host port and container port
-p <containter port>                        -> binds container port to a random host port
--name <name>                               -> name a container
-v <host directory>:<container directory>   -> bind host directory and container directory
-e <environment variables>                  -> define environment variables
-i                                          -> keeps STDIN open
-t                                          -> allocates a pseudo-tty
-i -t                                       -> used for interactive processes (like a shell)

Example:
docker run -it <container name> bash     -> gives access to shell inside the container
```
### exec
```
docker exec <container name> <command>   -> executes command on running container

options:
.......
-d                                       -> runs command in detached mode (background)
--privileged                             -> gives extended privileges to the command
-i                                       -> keeps STDIN open
-t                                       -> allocates a pseudo-tty
-i -t                                    -> used for interactive processes (like a shell)

NOTE: -t should not be used when the client is receiving its standard input from a pipe
```
### logs
```
docker logs <container name>   -> gives all the standard output and standard error logs

options:
.......
-f                             -> continues streaming the new output from the container
--details                      -> gives additional details
--tail <number>                -> shows given number of lines from the end
--since <timestamp>            -> shows logs since the timestamp
--until <timestamp>            -> shows logs until the timestamp

<timestamp> = (eg 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)

docker run <container name> --log-driver=<log driver name> -> launches the container with the specified log driver
docker run <container name> --log-driver=none              -> launches the container without logging the outputs
```
### stats
```
docker stats  -> displays metrics about the container
docket stats  -> displays metrics about all the containers
```
### ps
```
docker ps      -> lists running containers
docker ps -a   -> lists all containers
docker ps -s   -> also displays total file sizes
docker ps -q   -> lists the container id
```
### build
```
docker build -t <image name> .  -> builds the image from Dockerfile with the specified image name
docker build <url>              -> builds the image from the url
```
### Data Volumes
```
docker volume create <volume name>                                              -> creates a volume that can be used by containers

options:
........
--driver , -d                                                                   -> specify the volume driver name
--label                                                                         -> set a label(metadata) for a volume
--opt , -o                                                                      -> set driver specific options

docker volume ls                                                                -> lists the existing volumes

options:
........
--filter , -f                                                                   -> filter the results (e.g. 'dangling=true')
--format {{<placeholder>}}                                                      -> pretty-print volumes using a Go template
--quiet , -q                                                                    -> display only the volume names

docker volume inspect <volume name>                                             -> gives details about the specified volume

options:
........
--format {{<placeholder>}}                                                      -> pretty-print volumes using a Go template

docker volume rm <volume name>                                                  -> deletes the specified volume

options:
........
--force , -f                                                                    -> force delete without confirmation

docker volume prune                                                             -> deletes all unused volumes

options:
........
--format {{<placeholder>}}                                                      -> pretty-print volumes using a Go template
--force , -f                                                                    -> force delete without confirmation

docker run -v <host directory>:<container directory> --name <container name>    -> creates a volume (binds host and
-d <base image>                                                                    container directory) i.e data stored on
                                                                                   container directory presists on host directory
docker run -v <host directory>:<container directory>:ro --name <container name> -> creates a volume with "read-only" permission
```
### Shared Volumes
```
docker run --volumes-from <data container/volume name> <container name>         -> makes the data container/volume
                                                                                   accessible to the launched container
```

### Data Containers
```
docker create -v <volume directory> --name <data container name> <base image name> -> creates a data container with specified
                                                                                      volume location and name

docker export <data container name> > <any name>.tar                               -> to export the data container

docker import <any name>.tar                                                       -> to import the .tar data container
```
### Container Networks:
```
Links
.....
docker run --link <container to link>:<any alias> <base image>                 -> links the launched container to the one specified

Networks
........
docker network ls                                                              -> lists the available networks
docker network create <network name>                                           -> creates a network
docker network connect <network name> <container name>                         -> connects the specified container and network
docker network connect --alias <alias name> <network network> <container name> -> creates an alias to the container and 
                                                                                  connects it to the network
docker network disconnect <network network> <network network>                                                            
docker run --net=<network name> <base image>                                   -> launches the container and connects to the
                                                                                  specified network
docker network inspect <network name>                                          -> gives details about the containers 
                                                                                  connected to that network
```
### Ensuring uptime
```
docker run -d --restart=<restart policy> <container name>  -> restarts the container based on the restart policy
restart policy:
on-failure                                                 -> restarts the container if it exists due to an error
on-failure:<number>                                        -> restarts the container after specified number of failures
always                                                     -> always restarts the container if it stops
unless-stopped                                             -> always restarts the container until stopped manually
```
### Labelling
```
docker run -l <label> <container name>    -> adds a label to the container
docker run --label-file=<file name>       -> add labels from the specified file
```
### Load Balancing container
```
* launching a Load Balancing container
docker run -d -p 80:80 -e DEFAULT_HOST=<host name> -v /var/run/docker.sock:/tmp/docker.sock:ro --name nginx jwilder/nginx-proxy

-> launches a nginx-proxy container
-> mounts docker.sock (nginx uses this to listen for events an update nginx configuration)
-> DEFAULT_HOST makes sure that the specified machine handles the request if doesnt match any other hosts.

* launching a host container to handle the requests
docker run -d -p 80 -e VIRTUAL_HOST=<host name> <host container image>

-> launches a host container
-> VIRTUAL_HOST contains the "host name" of the container
```
### Docker Compose
```
docker-compose.yml structure
............................
container_name:
  property: value
    - or options

docker-compose up                                    -> launches the containers from docker-compose.yml
docker-compose ps                                    -> details of all launched containers
docker-compose logs                                  -> shows logs of all the containers in a single stream
docker-compose scale <service number>=<number>       -> increases the number of containers running a service (deprecated)
docker-compose up --scale <service number>=<number>  -> increases the number of containers running a service
```
### Docker Swarm
```
docker swarm init -> initialize swarm
docker swarm join --token <worker token> <host ip>:<port>  -> joins the swarm as a worker
docker swarm join --token <manager token> <host ip>:<port> -> joins the swarm as a manager
docker node ls                                             -> lists nodes in the swarm  
docker network create -d overlay <network name>            -> creates a new overlay network

docker service create <base image>                         -> creates a new service
options:
.......
-d                                                         -> detached mode
--network <network name>                                   -> attaches to the specified network
--replicas <number>                                        -> number of replicas of the service
--replicas-max-per-node <number>                           -> maximum number of replicas per node
--restart-condition <restart policy>                       -> defines restart policy

docker service ls
docker service scale <service name>=<number>               -> increases the number of containers running the service

docker node ps                                             -> lists tasks running on the manager node
docker node ps self                                        -> lists tasks running on the manager node leader
docker node ps $(docker node ls -q | head -n1)             -> lists tasks running on all the nodes
```
