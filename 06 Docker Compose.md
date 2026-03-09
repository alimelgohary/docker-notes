Docker compose is used when I want to have many containers with their own networking and volumes (e.g., Microservices) and run/stop them as a single unit.

Docker compose was a python module but now it's integrated directly into Docker
# docker-compose.yml
Describes what you need to do with docker compose

> [!NOTE]
> `.yml` files are like `.json` but without braces
> key: value
> key:
>> key: value
>> key: value

## `docker-compose.yml` main components
### 1. version (optional)
```docker-compose
version: "3.7"
```


### 2. services (can have multiple services)
For each service in `services` section:
- `image` or `build`
```dockerfile
# Build image from file
services:
  service_1:
	build: .
```

```dockerfile
# Use image
services:
  service_1:
	image: "my_image:latest"
```

- `command`: Command runs when the container starts
  
- `container_name`
  
- `ports` (target, published) exposed ports
```dockerfile
services:
  service_1:
	ports:
      - target: 80
	    published: 8080
```
OR
```
ports:
	- "8080:80"
```

- `environment`: env variables hardcoded or from .env file
```dockerfile
services:
  service1:
    environment:
      - DEBUG=true
      - DB_PASS=${DB_PASS}
```

```dockerfile
# Read env from custom file
services:
  service1:
    env_file:
      - ./file.env
```

- `depends_on` (if one service depends on another)
  Conditions available: `service_started`, `service_healthy` (require a health check), `service_completed_successfully`
```dockerfile
services:
  service1:
    depends_on:
      service2:
        condition: service_healthy
        restart: true # restart service1 if service2 is updated
```

- `networks`
  Used networks in that service
```dockerfile
services:
  service_1:
	networks: 
	  - mynet
```

- `volumes`
  Used volumes in that service (type, source, target)
  type: volume or bind
```dockerfile
services:
  my_service_1:
	volumes:
      - type: volume
	    source:
	    target:
```

- `healthcheck`: the command to run to check if the container is healthy
```dockerfile
services:
  service1:
    healthcheck:
	    test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB}"]
	    interval: 10s
	    retries: 5
	    start_period: 30s
	    timeout: 10s
```

- `restart` restart policy when failure

- `logging`: stdout/stderr logging of the container
```dockerfile
logging:
  driver: json-file
  options:
    max-size: 20m
    max-file: 3
```

- `deploy`: specify resource limits
```
deploy:
  resources: {limits: {memory: 512M}}
```
### 3. networks
You define networks inside it
```dockerfile
networks:
	mynet:
		#internal: true
```


### 4. volumes
```dockerfile
volumes:
	myvol:
```

---
## Full example
```dockerfile
version: "3.7"
services:
	service_1:
		build: .
		command: python app.py
		ports:
			- target: 5000
			  published: 5000
		networks:
			  - mynet
		volumes:
			- type: volume
			source: myvol
			target: /data
	service_2:
		image: "python:latest"
		command: python app.py
		ports:
			- target: 5000
			  published: 5000
		networks:
			  - mynet
		volumes:
			- vol1:/data
			- /dir:/app/data
volumes:
	myvol:
networks:
	mynet:
```

---
## Run the docker compose
`docker compose up`
`-d` detached
`--build` rebuild 
You find the 2 containers running.

#### Naming
If no container name is specified, their names is 
`directory-image1_name-1`,
`directory-image2_name-1`

#### Pinging 
They can ping each other ==by container name== or ==by service name==

---
## Run command inside a service
1. You can `exec` with container's hash
2. You can use service_name
   `docker-compose exec service_name command`

---
## Check services running in docker compose
```
docker-compose ps
```

---
## Stop the docker compose
`docker compose stop`

## Down
`docker compose down`
This deletes the containers/networks not volumes not images
`-v` : removes volumes too

