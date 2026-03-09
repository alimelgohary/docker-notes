# Images
A Docker Image is a READ-ONLY, layered template used to create containers. Think of it as a class— containers are instances of an image.

#### Image Tags
Naming: `user/repo_name:tag`

#### Change tag
This creates a new name to the same image (you should remove old image)
`docker tag <old name> <new name>`
#### Image Id
Each image has unique SHA256 hash

#### Image manifest
JSON file describing layers and their checksums. Ensures integrity

#### Build image
Executes instructions in dockerfile to create an image (current directory is sent to dockerd)
`docker build -t name:tag .`

#### Create image out of a container
`docker commit <container> <image>` not considered best practice

#### Image registries
Repositories of docker images: e.g., docker hub (public), AWS ECR, GCR (Google), GitHub Container Registry, Harbor (self-hosted)

#### Pull image
`docker pull user/repo_name:tag`
Pulls from docker hub by default

#### Push images
`docker push user/repo_name:tag`

#### List current local images
`docker images`

#### Remove image
Remove image: `docker rmi image_name`
Remove all unused/dangling images: `docker image prune`

# Container

#### Run container from image
`docker run <image_name>`
`--name` specify a name
`-d`: detached (in background)
`-p host_port:container_port`: Map ports
`-e key=value` add env variables on the fly
`-v` attach volume/bind mount
`--add-host name:ip` run and add an entry in hosts file inside that container
`--memory="512m" --cpus="1.5"`
`--restart` restart policy when failure
	options: 
	`on-failure`:  when container exits with error (can specify max retries)
	`always`: Always restart if it stops (if stopped manually, it will start again when daemon restarts)
	`unless-stopped`: it always restarts unless it's stopped manually
#### List containers
Running:`docker ps`
All: `docker ps -a`

#### Stop
`docker stop <container-name-or-id>`

#### Remove
`docker rm <container-name-or-id>`

#### Copy files in a container
`docker cp <source> cid:<destination>`

`Ctrl-p` followed by `Ctrl-q` is the default key sequence used to ==**detach from an interactive Docker container** without stopping it==.
# Debugging & Logs
Shows real-time logs
`docker logs -f <c_id>`

Run commands in the container
`docker exec -it <c_id> bash`

View container details
`docker inspect <c_id>`

CPU/Memory usage
`docker stats`

See processes
`docker top <c_id>`


