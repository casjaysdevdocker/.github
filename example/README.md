## 👋 Welcome to example 🚀  

example README  
  
  
## Install my system scripts  

```shell
 sudo bash -c "$(curl -q -LSsf "https://github.com/systemmgr/installer/raw/main/install.sh")"
 sudo systemmgr --config && sudo systemmgr install scripts  
```
  
## Automatic install/update  
  
```shell
dockermgr update example
```
  
## Install and run container
  
```shell
dockerHome="/var/lib/srv/$USER/docker/casjaysdevdocker/example/example/latest/rootfs"
mkdir -p "/var/lib/srv/$USER/docker/example/rootfs"
git clone "https://github.com/dockermgr/example" "$HOME/.local/share/CasjaysDev/dockermgr/example"
cp -Rfva "$HOME/.local/share/CasjaysDev/dockermgr/example/rootfs/." "$dockerHome/"
docker run -d \
--restart always \
--privileged \
--name casjaysdevdocker-example-latest \
--hostname example \
-e TZ=${TIMEZONE:-America/New_York} \
-v "$dockerHome/data:/data:z" \
-v "$dockerHome/config:/config:z" \
-p 80:80 \
casjaysdevdocker/example:latest
```
  
## via docker-compose  
  
```yaml
version: "2"
services:
  ProjectName:
    image: casjaysdevdocker/example
    container_name: casjaysdevdocker-example
    environment:
      - TZ=America/New_York
      - HOSTNAME=example
    volumes:
      - "/var/lib/srv/$USER/docker/casjaysdevdocker/example/example/latest/rootfs/data:/data:z"
      - "/var/lib/srv/$USER/docker/casjaysdevdocker/example/example/latest/rootfs/config:/config:z"
    ports:
      - 80:80
    restart: always
```
  
## Get source files  
  
```shell
dockermgr download src casjaysdevdocker/example
```
  
OR
  
```shell
git clone "https://github.com/casjaysdevdocker/example" "$HOME/Projects/github/casjaysdevdocker/example"
```
  
## Build container  
  
```shell
cd "$HOME/Projects/github/casjaysdevdocker/example"
buildx 
```
  
## Authors  
  
🤖 casjay: [Github](https://github.com/casjay) 🤖  
⛵ casjaysdevdocker: [Github](https://github.com/casjaysdevdocker) [Docker](https://hub.docker.com/u/casjaysdevdocker) ⛵  
