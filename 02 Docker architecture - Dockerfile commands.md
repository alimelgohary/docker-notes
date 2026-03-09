# Docker architecture
1. Docker client (CLI tool)
   (Tool to Interact with docker)
            ↓↓
         REST API
2. Docker Daemon (dockerd)
   (Manage images, containers, volumes, networks) it uses containerd
		    ↓↓
		 Push/Pull
3. Docker registry
   (Remote storage for docker images)

# Dockerfile
Text file with step-by-step instructions to build a docker image
Each structure creates a new layer

## Dockerfile Instructions
#### FROM
Base image to start from
e.g., `FROM node:18-alpine`
e.g., `FROM scratch`
e.g., `FROM registry/repo/image:tag`
e.g., `FROM image@sha256:9bca6........` 


#### WORKDIR
Makes directory if not exists and also `cd` to it (Sets the working directory)
e.g., `WORKDIR /app`
You can say  `WORKDIR child_dir` to change working directory to a child directory of `/app`

#### COPY
Copy files into the image e.g., `COPY . .`
e.g., `COPY file1 file2 destination/`
e.g., `COPY file* destination/`
e.g., `COPY ["file with space.py", "/app"]`

#### ADD
It's like `COPY` but with additional features like:
- Extracting `.tar` files
- fetch files from remote URL

#### RUN
Execute commands at build time
- The state of the container after a `RUN` command will be committed to the container image. 
- A Dockerfile can have many `RUN` steps that layer on top of one another to build the image.
e.g., `RUN pip install -r requirements.txt`
- Every `RUN` instruction creates a new layer. To keep images small, we chain commands.
e.g., `RUN command && command`

1. Shell form
	e.g., `RUN npm install`
	Shell form has environment variables/ piping / command chaining by default
2. Exec form
   e.g., `RUN ["executable", "argument"]` 
   does not involve a shell

#### EXPOSE
Document that the port(s) the app uses e.g., `EXPOSE 3000` (only a comment)

#### ENV
Global on all layers
Set environment variables 
1. set one variable `ENV KEY value` or `ENV KEY=value`
2. set many variables in one line `ENV KEY=value KEY2=value2 KEY3=value3`


#### LABEL
Metadata about the image `LABEL key="value"`
e.g., `LABEL maintainer="Ali"`

#### ARG
A variable used inside the dockerfile itself
arguments can be passed while building or has default values
e.g., 
```
ARG username=ali
USER $username
```

#### ENTRYPOINT
Main executable for the container e.g., `ENTRYPOINT ["./start.sh"]`

You can combine ENTRYPOINT with CMD
ENTRYPOINT for the main exe and CMD for the args


#### CMD
Default command when container starts e.g., `CMD ["node", "server.js]`
A Dockerfile will only use the final `CMD` defined. 
The `CMD` can be overridden when starting a container with `docker run $image $other_command`.

#### USER
sets the user for executing next commands

#### SHELL
Set the used shell for subsequent RUN, CMD, ENTRYPOINT instructions
Default for windows is `cmd` and Linux `sh`
e.g., `["/bin/bash", "-c"]`

#### HEALTHCHECK
the command to run to check if the container is healthy
```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --start-period=20s --retries=3 \
    CMD wget -qO- http://127.0.0.1:8000/api/health || exit 1
```

## Example
```
# Use official Node.js Alpine image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files first (layer caching!)
COPY package*.json ./
RUN npm ci --only=production

# Copy rest of app code
COPY . .

# Expose port and set entrypoint
EXPOSE 3000

CMD ["node", "app.js"]
```
---

# .dockerignore
A file like .gitignore and is used to ignore files from being copied by a COPY instruction