# Virtualization
Virtualization is the creation of software-based (virtual) representations of physical hardware resources — including servers, storage, and networks — allowing multiple isolated environments to run on a single physical machine simultaneously.

#### Structure
Physical host \=\> Hypervisor \=\> VM (has its own OS/Apps) \=\> Isolation
#### Benefits of it
1. Multiple VMs/Apps on 1 physical server
2. High server utilization \=\> Hardware cost reduction
3. Provisioning new VM takes minutes
4. Easy to replicate (snapshot and clone)
5. Full isolation between workloads (HW level isolation)
6. Disaster recovery & business continuity
7. Mature, battle-tested technology
8. Best for legacy apps

#### Cons
1. Each VM Full OS: High Storage usage/slow startup/high memory usage
2. Virtualization layer performance overhead
3. Not ideal for microservices
4. Licensing costs can be significant
---
### Hypervisor types
1. Type 1: Bare metal
   * H/W \=\=\=\=\=\=\=\=\=\> Hypervisor \=\=\=\=\=\> VMs
   - Best performance/stability/security (e.g., ESXi, KVM, Xen, Hyper-V Server)

2. Type 2: Hosted
   * H/W \=\> Host OS \=\> Hypervisor(as app) \=\> VMs
   - Easier to install/configure (e.g., Virtual Box, VMware Workstation, Parallels)

---
# Containerization
A Lighter, Faster Way to Package & Run Applications
e.g., Docker, containerd, CRI-O, Podman
#### Container:
A container is a lightweight, standalone, executable package that includes EVERYTHING needed to run an application: code, runtime, libraries, environment variables, and config files — but shares the HOST OS kernel

#### Structure of container
H/W \=\> Host OS (shared Kernel) \=\> Container runtime (e.g., docker) \=\> Minimal OS filesystem \=\> App runtime \=\> App dependencies \=\> Your application code 

and it has a Thin writable layer (runtime changes)

---
#### Benefits
1. Lightweight (shares HOST OS Kernel)
2. Portable
3. Fast starting time (ms)
4. Isolated (but only process-level) (Also kernel vulnerabilities can expose every container)
5. Near-native performance
6. Can have hundreds/thousands container per host
7. Best for microservices
---
### How it works?
It uses 2 Linux kernel features
1. Namespaces
2. cgroups

#### Namespaces
   - Linux kernel had this feature long time ago
   - Used to provide isolation & security for testing purposes
   - You can give a namespace its own POV for the system
     Processes in a namespace can only see each other not other namespaces
	   - NET: You can isolate it from using network at all (or have its own network)
	   - MNT: its own filesystem
	   - PID: It can run with PID 1 (It thinks it's the only thing running)
	   - UTS: its own hostname
	   - IPC: signals between namespaces
	   - User: (let a process be root on its own namespace)
   - By default, your Linux uses a default namespace to run all processes 


#### cgroups
Limit/control usage of
1. CPU
2. Memory
3. I/O
4. Network (bandwidth & priority)
5. Device access
6. PID (number of processes)
---
### Container Image Layering
1. Each layer is read-only and cached
2. Layers is reused to save disk space
3. Fast builds (only changed layers are rebuilt/pulled)
4. Containers add thin writable layer on top

#### Node example
Layer 1: FROM node:18-alpine (Base) — 48.3 MB — sha256: 1e2b..

Layer 2: WORKDIR /app                      —   0 MB    — sha256: 9f4a...

Layer 3: COPY package.json               —  0.02 MB — sha256: 2c8d...

Layer 4: RUN npm install                   —  45.3 MB  — sha256: 7b19...

Layer 5: COPY . .                                 —   2.1 MB   — sha256: a3f2...


When a container makes changes in an old layer, docker copies that file and put in the writable layer and then write to it. Original layers are read-only

- This has performance overhead in write-intensive apps (frequent update existing files)
	- You can otherwise use volumes for near native I/O performance



> [!NOTE] Efficient disk usgae
> Disk space of 100 containers from the same image  = Initial image size + 100 * each writable layer of each container (only)
> $$Size_{total} = Size_{image} + \sum_{i=1}^{100} w_i$$

---
