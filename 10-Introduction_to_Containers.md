# Introduction to Containers

```shell
dnf install container-tools

podman info
podman search python
podman images # list installed images

skopeo login 
skopeo docker://registry.redhat.io/rhel8/python-39

# Run first container
podman run -d --name webserver -p 8080:8080 registry.access.redhat.com/ubi9/httpd-24
podman create 
podman start
podman stop
podman ps -a

# Build a container
vi Containerfile
###########
FROM registry.access.redhat.com/ubi9/python-39

LABEL maintainer="RHCSA training"
LABEL description="Simple Web server for RHCSA drills"

COPY index.html /app/index.html

EXPOSE 8000

WORKDIR /app

CMD ["python","-m","http.server"]
##########

touch index.html
echo "Hello World!" > index.html 

podman build -t my-image .
podman run -d --name simple-webserver -p 8000:8000 python-webserver

# Push local image to quai.io
podman login -u='sramzln' -p='mHlUheJm6+DM3ttBDfbarWh33XwtCJQu04/zKMaIvnVnwjL8a5HdESE7Ipg69LfOZrWoCL4Mj+c+vkebUbP4+A==' quay.io
podman images
# Tag th image on format quai.io
podman tag localhost/python-webserver:latest quay.io/sramzln/simple-webserver:latest
podman tag my-image:latest my-image:v1.0
podman push quay.io/sramzln/simple-webserver:latest

# Run in the background
podman run -d --name simple-webserver -p 8000:8000 python-webserver

# Interact with the container
podman run -ti simple-webserver:latest /bin/bash

podman inspect my-app
podman logs my-app

podman rmi # remove images

podman attach
podman cp
podman exec
podman generate
podman info
podman version

## Run container as a systemd service
~/.config/systemd/user # for a standard user
/etc/systemd/system # for root user

systemctl --user start container-webserver.service
systemctl --user enable container-webserver.service

loginctl enable-linger
```
