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
`docker inspect -f"{{ .NetworkSettings.Networks.nat.IPAddress }} <NAME or CONTAINER ID>`
`docker inspect -f"{{ .NetworkSettings.IPAddress }} <NAME or CONTAINER ID>`
`docker image inspect`

To see which ports the docker container is mapping too
`docker port <NAME or CONTAINER ID>`
_output:_ 8080/tcp -> 0.0.0.0:8080
_Explanation:_ 8080 on the container will map to 8080 on the host
