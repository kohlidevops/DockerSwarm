# Docker Swarm

## Why Docker Swarm?

○ How to Scale Containers?

○ How to manage Containers or re-create if they Fails/Crash?

○ How to Upgrade the Service with Zero DownTime?

○ How to Manage Containers on VMs, Nodes?

## What is Docker Swarm?

➢ Docker Swarm is a clustering and scheduling tool for Docker containers.

➢ Swarm is Docker’s native support for orchestrating clusters of Docker engines.

➢ Orchestration : Define nodes. Define services. Set how many nodes you want to run and where, and you’re done.

➢ At a high level, Swarm takes multiple Docker Engines running on different hosts and lets you use them together.

➢ Docker Swarm : Docker Swarm have Two Type of Nodes Master(Manager) and Worker.

➢ Every swarm starts out with one manager node designated as the leader.

➢ Swarm is highly available thanks to its implementation of the Raft algorithm.

➢ Raft Algo : Raft is a consensus algorithm designed to achieve fault tolerance in distributed systems. The leader node is constantly checking in with its fellow manager nodes and syncing their states.

➢ Nodes and Roles: In Raft, a cluster consists of multiple nodes, and each node can have one of three roles: leader, follower, or candidate. There is one leader at a time, and followers replicate the leader's actions.

➢ Leader Election: Algorithm starts with all nodes in the follower state.

    ○ If a follower doesn't receive communication from a leader for a certain period (election timeout), it transitions to the candidate state and requests votes from other nodes to become the leader.
    
    ○ If a candidate receives votes from the majority of the nodes, it becomes the leader.

➢ Handling Failures:

    ○ If a leader fails, a new leader is elected through the election process.

    ○ Nodes can detect inconsistencies and missing entries in their logs and update them by replicating the logs from the leader.

➢ Task Scheduling

➢ Load Balancing

➢ Rolling Updates

➢ Security

➢ Docker Swarm : The cluster management and orchestration features embedded in the Docker Engine are built using swarmkit.

➢ A swarm consists of multiple Docker hosts which run in swarm mode and act as managers (to manage membership and delegation) and workers (which run swarm services).

➢ Host : Docker host can be a manager, a worker, or perform both roles.

➢ Service : When you create a service, you define its optimal state (number of replicas, network and storage resources available to it, ports the service exposes to the outside world, and more).

➢ Docker Swarm :Docker Swarm maintains the Service Desired State. For instance, if a worker node becomes unavailable, Docker schedules that node’s tasks on other nodes.

➢ Task : Task is a running container which is part of a swarm service and managed by a swarm manager.

➢ Nodes : A node is an instance of the Docker engine participating in the swarm.

➢ You can run one or more nodes on a single physical computer or cloud server, but production swarm deployments typically include Docker nodes distributed across multiple physical and cloud machines.

➢ To deploy your application to a swarm, you submit a service definition to a manager node. The manager node dispatches units of work called tasks to worker nodes.

➢ Manager nodes also perform the orchestration and cluster management functions required to maintain the desired state of the swarm. Manager nodes elect a single leader to conduct orchestration tasks.

➢ Worker nodes receive and execute tasks dispatched from manager nodes.

➢ Service : A service is the definition of the tasks to execute on the manager or worker nodes.

➢ When you create a service, you specify which container image to use and which commands to execute inside running containers.

➢ Task : A task carries a Docker container and the commands to run inside the container.

➢ Once a task is assigned to a node, it cannot move to another node. It can only run on the assigned node or fail.

➢ Load Balancing : Swarm manager uses ingress load balancing to expose the services you want to make available externally to the swarm.

➢ External components, such as cloud load balancers, can access the service on the Published Port of any node in the cluster whether or not the node is currently running the task for the service.

➢ All nodes in the swarm route ingress connections to a running task instance.

## To initialize the docker swarm in Docker host

```
docker swarm init
or
docker swarm init --advertise-addr <public or private ip>
```

![image](https://github.com/user-attachments/assets/855230bf-f44c-4066-9e2f-babcddea4dac)

**Docker swarm commands**

```
docker swarm --help
docker service --help
//Will list all of the commands and its usage
```

**To check the docker service in docker swarm**

```
docker service ls
```

**To create a new service in docker swarm**

```
docker service create alpine ping www.google.com
docker service ls
```

![image](https://github.com/user-attachments/assets/c625a1a2-64e9-468d-88db-88fbf9384922)

**How many containers are running in my service?**

```
docker service ls
docker service ps <service-id>
```

![image](https://github.com/user-attachments/assets/65802a0d-6e49-4ed3-a81e-782d7ae87746)

**To inspect the container in the service**

```
docker service inspect <service-id>
```

![image](https://github.com/user-attachments/assets/4dee9c5b-fe9a-405c-9d6b-921457a12fa8)

**To Scaleup the services using replicas**

```
docker service update <service-id> --replicas <number-of-replicas>
docker service ls
docker service ps <service-name>
```

![image](https://github.com/user-attachments/assets/ef880cc6-0066-4bff-bdf6-1146e829c7af)

**To remove one container and check the replica**

```
docker service ps <service-id>
docker rm -f <container-id>
docker service ps <service-id>
docker service ls
docker service inspect <service-id>
```

![image](https://github.com/user-attachments/assets/4ee7f2f9-6ae3-44e5-8292-066aabb48670)

New container has been placed as replicas count in docker swarm service (Its maintain the desired state of your service)

![image](https://github.com/user-attachments/assets/043d4f2d-260d-4c5c-a90c-0777aaee1b31)

**To roll back the service**

```
docker service ls
docker service rollback <service-id>
docker service ls
```

It will back to the previous state from the current state. The containers are scale-down from 5 to 1 as we defined earlier.

![image](https://github.com/user-attachments/assets/ec55054c-e5e4-4e19-97bd-f72df9611ba7)

## To Setup Docker swarm cluster

Assuming my current node as Docker swarm manager and launch 3 more EC2 instances with ubuntu-22 as worker nodes

![image](https://github.com/user-attachments/assets/43ad01c8-4483-4468-8013-4e5fb1b8bba9)

**Install Docker engine on all other nodes**

```
https://docs.docker.com/engine/install/ubuntu/

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
sudo chmod 777 /var/run/docker.sock
docker run hello-world
```

**To print the docker swarm join-token in manager node**

```
docker swarm join-token manager
```

![image](https://github.com/user-attachments/assets/aae1575d-97b0-430d-8469-6ad20becd984)

**To list the node in docker swarm manager**

```
docker node ls
```

![image](https://github.com/user-attachments/assets/702640b1-1ab1-4998-bcef-de59abd1d039)

**To join the docker node-01 to manager node using join-token**

```
//To copy the join-token from manager node and execute on docker node-01
docker swarm join --token <token-id> 172.31.28.57:2377
```

![image](https://github.com/user-attachments/assets/3d8c6ac5-6cb1-48d4-b2b1-8101552dad9e)

**To list the nodes in docker swarm manager**

```
docker node ls
```

![image](https://github.com/user-attachments/assets/827b274d-0980-47ef-9bec-1671fff0a1d0)

As of now, new docker node-01 has been joined as manager and it is Reachable state.

