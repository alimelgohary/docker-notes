# Types
### Bridge
Default if not network specified.

When you create a network, its driver is bridge
Provides DNS resolution 
	Containers on the same bridge network can call each other by container names


| Feature           | Default Bridge Network                                                                                                                 | User-Defined Bridges Network                                                                                                          |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **Communication** | Containers can only communicate by IP address (unless using the legacy `--link` flag).                                                 | Containers can communicate by name or alias, which is better for service discovery.                                                   |
| **Isolation**     | All containers started without a specified network connect to it by default, increasing communication risk between unrelated services. | Provides scoped, better isolation; only containers explicitly connected to the network can communicate.                               |
| **Flexibility**   | Containers cannot be connected or disconnected on the fly; requires stopping and re-creating the container.                            | Containers can be connected or disconnected from networks on the fly during their lifetime.                                           |
| **Configuration** | Configuration is done outside of Docker (via `daemon.json`) and requires a Docker restart, applying to all containers on it.           | Created and configured with the `docker network create` command, allowing specific settings (subnet, gateway, etc.) for each network. |

### Host
`docker run --network host`
- Shares hostname of Host OS
- Shares networks of the host
- Does not require port mapping (if 2 use port 80, the second one will **fail to start**)

### None
Disconnected from any network
`--network none`


### Macvlan
Assigns a unique MAC address to a container, making it appear as a physical device on the network. This is ideal for legacy apps that need to be on a specific VLAN.

### Overlay
in multi-host docker environments


# Create network
`docker network create <network name>`
Options:
	`--subnet 10.0.0.0/16` change default subnet
	`--internal` disconnected from internet
# Assign network to container
`docker network connect/disconnect <network> <container>`


## Commands
- `docker network inspect <name>`
Crucial for seeing which containers are currently attached and what their internal IP addresses are.
- **`docker network prune`**: Removes all unused networks to keep your `ip addr` output clean.