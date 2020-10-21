# Docker Security

### Cgroups and Namespaces
```
docker run <image name>  -> 
options:
.......
--cpu-shares             -> set the CPU shares
--cpuset-cpus            -> set the CPUs shares in which to allow execution (0-3, 0,1)
--memory-reservation     -> set the memory soft limit
--kernel-memory          -> set the kernal memory limit
--blkio-weight           -> Block IO weight (relative weight) accepts a weight value between 10 and 1000
--device-read-iops       -> Limit read rate (IO per second) from a device
--device-write-iops      -> Limit write rate (IO per second) to a device

Namespace examples
..................
Cgroup      CLONE_NEWCGROUP   Cgroup root directory
  IPC         CLONE_NEWIPC      System V IPC, POSIX message queues
  Network     CLONE_NEWNET      Network devices, stacks, ports, etc.
  Mount       CLONE_NEWNS       Mount points
  PID         CLONE_NEWPID      Process IDs
  User        CLONE_NEWUSER     User and group IDs
  UTS         CLONE_NEWUTS      Hostname and NIS domain name

usernamespaces
..............
*Add the line to /etc/docker/daemon.json
 
 "userns-remap": "1000:1000"

*Restart docker service
 
 service docker restart
```
### seccomps
```
docker run --rm -it --security-opt seccomp:<policy> <image name>  -> Runs the container with seccomp policy
```
### no-new-privilege
```
docker run --security-opt=no-new-privileges <image name>  -> prevents the container from getting new privileges
```
### apparmor and bane
```
apparmor_parser -r -W <profile name>                            -> Installing the profile
docker run --security-opt apparmor=<profile name> <image name>  -> Launching the container with apparmor profile
aa-status                                                       -> Check the status of apprmor profiles

Bane
....
bane sample.toml                                                -> Convert .toml to apparmor porfile
```
### ONVAULT
```
*First we should have a Dockito Vault server (HTTP server) running at http://172.17.0.1:14242 (serves the private keys) and have the location of private keys mounted as a volume.
 
 docker run -d -p 172.18.0.1:14242:3000 -v <private key location - host>:<container directory> dockito/vault

*We have to build the image along with ONVAULT utility installed. So the Docker file shouldd contain the following.
 
 RUN apt-get update -y && \
     apt-get install -y curl && \
     curl -L $(ip route|awk '/default/{print $3}'):14242/ONVAULT > /usr/local/bin/ONVAULT && \
     chmod +x /usr/local/bin/ONVAULT

*Then we can use keys using
 
 RUN ONVAULT <command that uses keys> --unsafe-perms
```
### Hashicorp valut
```
*Hashicorp vault uses consul. So consul should be run. Running a consul container agent as dev
 
 docker run -d --name consul -p 8500:8500 consul:v0.6.4 agent -dev -client=0.0.0.0

*creating a data container to store the consul config 
 
 docker create -v /config --name config busybox

*copying the config to the data container
 
 docker cp vault.hcl config:/config/

*Running the vault container and using the config from the data container
 
 docker run -d --name vault-dev --link consul:consul -p 8200:8200 --volumes-from config cgswong/vault:0.5.3 server -config=/config/vault.hcl

*We have to do some config to proxy the commands to the vault and set an address for the vault. We also create an alias to make the process of making calls to the CLI easier
 
 alias vault='docker exec -it vault-dev vault "$@"'
 export VAULT_ADDR=http://127.0.0.1:8200

*After the config, we have to initialize Vault.
 
 vault init -address=${VAULT_ADDR} > keys.txt 

*The "keys.txt" file has info that lets us access the vault

*To read from the vault, we have to unseal the vault (construct the master key). We'll be using three of the five keys in "keys.txt"
 
 vault unseal -address=${VAULT_ADDR} $(grep 'Key 1:' keys.txt | awk '{print $NF}')
 vault unseal -address=${VAULT_ADDR} $(grep 'Key 2:' keys.txt | awk '{print $NF}')
 vault unseal -address=${VAULT_ADDR} $(grep 'Key 3:' keys.txt | awk '{print $NF}')

*Checking the status. It should now show "Sealed: false"
 
 vault status -address=${VAULT_ADDR}

*Vault tokens are needed to communicate with the vault and is present in "keys.txt". By storing it in a variable we can use it during API calls
 
 export VAULT_TOKEN=$(grep 'Initial Root Token:' keys.txt | awk '{print substr($NF, 1, length($NF)-1)}')

*Logging into the vault
 
 vault auth -address=${VAULT_ADDR} ${VAULT_TOKEN}

*Storing and reading the data
 
 vault write -address=${VAULT_ADDR} secret/api-key value=12345678
 vault read -address=${VAULT_ADDR}   secret/api-key

*Using HTTP API
 
 curl -H "X-Vault-Token:$VAULT_TOKEN" -XGET http://docker:8200/v1/secret/api-key
```
### libsecret
```
*Launch libsecret plugin using nohup
 
 nohup docker-volume-libsecret --addr $VAULT_ADDR --backend vault --store-opt token=$VAULT_TOKEN</dev/null &> libsecretlogs &

*Start the container with volume diver as libsecret
 
 docker run -ti --rm --volume-driver libsecret -v secret/app-1/:/secrets <base image>
```
