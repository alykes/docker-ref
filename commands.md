To install docker desktop to another drive, create links on your OS before installation.
```
mklink /j "C:\ProgramData\Docker" "E:\Docker\ProgramData\Docker"
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
