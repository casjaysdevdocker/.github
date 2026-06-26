## 👋 Welcome to example-new 🚀  

example-new README  
  
  
## Install my system scripts  

```shell
 sudo bash -c "$(curl -q -LSsf "https://github.com/systemmgr/installer/raw/main/install.sh")"
 sudo systemmgr --config && sudo systemmgr install scripts  
```
  
## Automatic install/update  
  
```shell
dockermgr update example-new
```
  
## Install and run container
  
```shell
dockerHome="/var/lib/srv/$USER/docker/casjaysdevdocker/example-new/latest/volumes"
mkdir -p "$dockerHome"
git clone "https://github.com/dockermgr/example-new" "$HOME/.local/share/CasjaysDev/dockermgr/example-new"
cp -Rfva "$HOME/.local/share/CasjaysDev/dockermgr/example-new/volumes/." "$dockerHome/"
docker run -d \
--restart always \
--privileged \
--name casjaysdevdocker-example-new-latest \
--hostname example-new \
-e TZ=${TIMEZONE:-America/New_York} \
-v "$dockerHome/data:/data:z" \
-v "$dockerHome/config:/config:z" \
-p 80:80 \
casjaysdevdocker/example-new:latest
```
  
## via docker-compose  
  
```yaml
services:
  ProjectName:
    image: casjaysdevdocker/example-new
    container_name: casjaysdevdocker-example-new
    environment:
      - TZ=America/New_York
      - HOSTNAME=example-new
    volumes:
      - "/var/lib/srv/$USER/docker/casjaysdevdocker/example-new/latest/volumes/data:/data:z"
      - "/var/lib/srv/$USER/docker/casjaysdevdocker/example-new/latest/volumes/config:/config:z"
    ports:
      - 80:80
    restart: always
```
  
## Get source files  
  
```shell
dockermgr download src casjaysdevdocker/example-new
```
  
OR
  
```shell
git clone "https://github.com/casjaysdevdocker/example-new" "$HOME/Projects/github/casjaysdevdocker/example-new"
```
  
## Build container  
  
```shell
cd "$HOME/Projects/github/casjaysdevdocker/example-new"
buildx 
```
  
## Authors  
  
🤖 casjay: [Github](https://github.com/casjay) 🤖  
⛵ casjaysdevdocker: [Github](https://github.com/casjaysdevdocker) [Docker](https://hub.docker.com/u/casjaysdevdocker) ⛵  

