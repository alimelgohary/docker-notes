Containers are EPHEMERAL — when they removed, all data inside is LOST. Volumes solve this by mounting storage that persists outside the container lifecycle.


# Named volumes
Managed by docker and stored in `/var/lib/docker/volumes/`
`-v volume_name  :   /persisted/path/inside/container`

`docker run -v   mydata:/data  nginx`

- Use in production

# Bind mounts
Mount any host directory to use inside
`-v /host/path  :   /mount/location`

`docker run -v $(pwd):/app node`

- Use in development for testing purposes


# tmpfs
Stored in host's memory only. Best for sensitive temp data

`docker run --tmpfs /tmp nginx`
