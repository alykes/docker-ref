To install docker desktop to another drive, create links on your OS before installation.
```mklink /j "C:\ProgramData\Docker" "E:\Docker\ProgramData\Docker"
mklink /j "C:\ProgramData\DockerDesktop" "E:\Docker\ProgramData\DockerDesktop"
mklink /j "C:\Program Files\Docker" "E:\Docker\Program Files\Docker"
mklink /j "C:\Users\<USER>\AppData\Local\Docker" "E:\Docker\Users\<USER>\AppData\Local\Docker"
mklink /j "C:\Users\<USER>\AppData\Roaming\Docker" "E:\Docker\Users\<USER>\AppData\Roaming\Docker"
mklink /j "C:\Users\<USER>\AppData\Roaming\Docker Desktop" "E:\Docker\Users\<USER>\AppData\Roaming\Docker Desktop"
```

Grab system information from the current system
`docker system info `

Pull image from docker hub
`docker image pull redis`

To check on the hash on a docker image
`docker image ls --digests`

To look at how the image was built (any nonzero values creates a layer, zero values adds to the json configuration file)
`docker history redis`

To inspect the image, gives configuration and layers
`docker image inspect redis`

Build an image (after Dockerfile has been created)
`docker image build -t webapp .`
 - -t webapp is the name you would like to tag the image with

Building from a git repo
`docker image build -t web-app https://github.com/nigelpoulton/psweb.git`

To run the image that you just built
`docker container run -d --name website -p 8080:8080 wedapp`
 - <website> is any cosmetic name
 - -p host:container
 - <webapp> is the name of the image

To run a container in detached mode
`docker container run --rm -d --name web -p 8080:8080 nginx`

To run a container and log into it via an Interactive Terminal
`docker container run -it alpine sh`

To log into a shell on a running container via an Interactive Terminal
`docker container exec -it <NAME or CONTAINER ID> sh`

To exit a running container gracefully without killing it
`Ctrl + P + Q`

To run a command (eg. ls -l) on a running container without an interactive shell
`docker container exec <NAME or CONTAINER ID> ls -l`

To start up a container in detached mode and sleep it for 1 day
`docker container -d alpine sleep 1d`

To start a container use the Name or the CONTAINER ID
`docker container start <NAME or CONTAINER ID>`

To stop a container use the NAME or CONTAINER ID as the command looks for uniqueness
`docker container stop <NAME or CONTAINER ID>`

To remove all container in a one-liner
`docker container rm $(docker container ls -aq) -f`

Each image has a default process it will run if you don't specify one when at run-time
eg, alpine will give you a shell prompt
`docker container run -it alpine`

To see the default process, issue the following command and look for *Cmd:*
`docker inspect -f"{{ .Config.Cmd }} <NAME or CONTAINER ID>`
`docker inspect -f"{{ .Config.Entrypoint }} <NAME or CONTAINER ID>`
Small caveat:
  CMD: Run time arguments override CMD instructions
  ENTRYPOINT: Run time arguments are appended to ENTRYPOINT

Finding entries via the inspect command
`docker inspect -f"{{ .NetworkSettings.Networks.nat.IPAddress }} <NAME or CONTAINER ID>`
`docker inspect -f"{{ .NetworkSettings.IPAddress }} <NAME or CONTAINER ID>`
`docker image inspect`

To see which ports the docker container is mapping too
`docker port <NAME or CONTAINER ID>`
_output:_ 8080/tcp -> 0.0.0.0:8080
_Explanation:_ 8080 on the container will map to 8080 on the host

### Swarm Mode

On the first manager, initialise the Swarm
`docker swarm init`
`docker swarm init --advertise-addr x.x.x.x`

To point the manager to an external CA
`docker swarm init --external-ca <external-ca>`

To retrieve the manager token
`docker swarm join-token manager`

To retrieve the worker token
`docker swarm join-token worker`

To add another manager to the swarm
`docker swarm join --token <manager token> <IP of a Manager>`

To add another worker to the swarm
`docker swarm join --token <worker token> <IP of a Manager>`

1st part tells you that it's a swarm token
2nd part is hash of the cluster cert
3rd part identifies if it will be a worker or a manager

To rotate the token for the workers (Must be run on a manager) - Exisiting membership is unaffected
`docker swarm join-token --rotate worker`

To look at the certificate on a node
`openssl x509 -in /var/lib/docker/swarm/certificates/swarm-node.crt -text`
Organisation is the swarm ID
OU is the node's role
CN is the cryptographic node id

To update the certificate expiry
`docker swarm update --cert-expiry 90d`

To list the nodes in a swarm
`docker node ls`

To list services in the Swarm
`docker service ls`

To list the nodes on which the container 'app' is running in the Swarm
`docker service ps app`

Services are pingable on the same network scope
`ping app`

To inspect details on a service
`docker service inspect app --pretty`

Quick way to delete services
`docker service rm $(docker service ls -q)`

*AutoLocking the Swarm*

You can auto-lock the swarm on init
`docker swarm init --autolock`

You can auto-lock the swarm via an update command
`docker swarm update --auto-lock=true`
This will output an unlock key that you need to safeguard

To rejoin a manager to the swarm when the docker service is restarted, you need to unlock it
`docker swarm unlock`
  - the provide the key
(workers are not affected)

### Docker Networking
List the Networks
`docker network ls`

To inspect the Network
`docker network inspect bridge`

You can create an overlay (multi-host network) with the following command
`docker network create`

To build on the bridge driver (which is default anyway) add the -d [drvier] switch
`docker network create -d bridge harbour-bridge`

To use an overlay Network
`docker network create -d overlay overnet`
- If you perform a network ls, you will see it is scoped to the swarm

To encrypt the data plane
`docker network create -o encrypted`

To crate a MACVLAN (transparent in Windows)
 - Gives the ability for the containers to be visible on the corporate network (or some other segment)
`docker network create  `

To add a container to a specific Network
`docker container run --rm -d --network harbour-bridge alpine sleep 1d`

To add a container to the overlay Network in a swarm
`docker service create -d --name app --replicas 2 --network overnet slpine sleep 1d`
`docker service ps`
`docker service ps app`
`docker network inspect overnet`

Quick way to clean up services
`docker service rm $(docker service ls -q)`

### Docker Storage

To list volumes
`docker volumes ls`

To create a volume
`docker volume create vol1`

To inspect the volume
`docker volume inspect vol1`
 - Shows you where the volume is located

To delete the volume
`docker volume rm <NAME>`

Run a container and mount a persistent volume
`docker container run -dit --name myvoltest --mount source=myvol,target=/vol alpine:latest`

Add the *myvol* volume that was created in the previous command to another container
`docker container -dit --name myvoltest2 --mount source=myvol,target=/app nginx:latest`

To delete a volume
`docker volume rm myvol`
 - If the volume is in use by the container, you won't be able to delete it

### Docker Secrets
