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

**To print the docker swarm join-token for manager in manager node**

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
//To copy the manager join-token from manager node and execute on docker node-01
docker swarm join --token <token-id> 172.31.28.57:2377
```

![image](https://github.com/user-attachments/assets/3d8c6ac5-6cb1-48d4-b2b1-8101552dad9e)

**To list the nodes in docker swarm manager**

```
docker node ls
```

![image](https://github.com/user-attachments/assets/827b274d-0980-47ef-9bec-1671fff0a1d0)

As of now, new docker node-01 has been joined as manager and it is Reachable state.

Docker engine has been installed on docker node-02 and node-03.

**To print the docker swarm join-token for worker in manager node**

```
docker swarm join-token worker
```

![image](https://github.com/user-attachments/assets/f8bbf3c3-9921-4c2d-8f46-c9c2940053f5)

**To join the docker node-02 and node-03 to worker node using join-token**

```
//To copy the worker join-token from manager node and execute on docker node-02 and node-03
docker swarm join --token SWMTKN-1-1dpm359sovsykgwzaw0pi60q0w55bbwe21wusw66eacsuq0cj9-d8hjxj41iilkk057fp4zy8ygp 172.31.28.57:2377
```

![image](https://github.com/user-attachments/assets/62f37d24-be1d-49e4-915e-88d2283c7737)

![image](https://github.com/user-attachments/assets/88c56153-3e37-4e7c-91e4-f858f48fa066)

**To list the nodes in docker swarm manager**

```
docker node ls
```

![image](https://github.com/user-attachments/assets/6c59e84e-cfec-4484-abf5-a4b0488b1f1b)

As per image, docker-host is Leader, docker-01 is Reachable and docker-02 and docker-03 are worker.

**To promote a docker-02 as manager in docker-03 that is worker node**

If you are promote docker-02 node from any worker nodes, then it wont promote.

As per our environment, docker-02 and docker-03 are worker nodes. Can we try to promote docker-02 as manager in docker-03? Will it work? No It wont work

![image](https://github.com/user-attachments/assets/96a8feb9-ba2f-4301-835f-ed2eb818c2ee)

**To promote a docker-02 as manager in docker-01 that is manager node**

If you are promote docker-02 node from any manager nodes, then it should get promote.

Try to promote the docker-02 as manager in docker-01. It shoudl work as below image.

![image](https://github.com/user-attachments/assets/b964a53e-151f-4909-98ea-e171488ece5e)

```
docker node promote docker-02
```

Now docker-02 has been joined to manager node.

![image](https://github.com/user-attachments/assets/1b30bdb7-223e-4d5b-9dcf-d7449631d971)

**To demote the docker-01 in docker-02 node**

```
docker node demote docker-01
```

![image](https://github.com/user-attachments/assets/00487a69-3571-46b1-9ea6-30a4c880f0ed)

**To list the docker nodes**

```
docker node ls
```

![image](https://github.com/user-attachments/assets/4280e9e3-2d63-4ea4-853a-021475ef23cd)

Now docker-01 has been demoted from manager role.

docker-01 and 03 are worker nodes and docker-01 and host are manager node.

So we dont need to login to host manager node to run anay management commands. Even you can login into any manager nodes and run any management commands.

**To create a service with replicas in docker swarm cluster**

SSH to host manager node

```
docker service create --replicas 10 alpine ping www.google.com
```

![image](https://github.com/user-attachments/assets/c4e8dd11-287a-4056-a3e9-2c27403bd05c)

To list the service

```
docker service ls
```

**To list the containers are running which is associated with service**

```
docker service ps <service-name>
```

![image](https://github.com/user-attachments/assets/44e9609e-5379-4b9e-a3c2-0cd3975dc659)

All the containers are deployed into all the docker swarm nodes.

**To list the containers which are associated to specific node**

SSH to particular node-01 for example

```
docker container ls
```

![image](https://github.com/user-attachments/assets/80eceaac-426c-44ac-9dbf-ec54783ad090)

## Docker Swarm and Networking Basics

**Docker Swarm:** This is a tool for managing a cluster of Docker hosts (servers) and running containers on them.

**Overlay Network:**
        
Purpose: It creates a network that spans multiple Docker hosts (nodes) in a Swarm.

Function: Allows containers on different hosts to communicate with each other as if they were on the same local network.

**Key Networks in Docker Swarm**

When you set up Docker Swarm, two special networks are created:

**Ingress Network:**

    What It Is: An overlay network automatically created by Swarm.
    
    Function: Handles routing traffic to your services in the Swarm.
    
    Usage: If a Swarm service isn't connected to a custom (user-defined) overlay network, it will use the Ingress network.

**Bridge Network (specifically docker_gwbridge):**

    What It Is: A network that connects the Docker nodes to each other.
 
    Function: Allows communication between Docker containers on different nodes.

**Rules for User-Defined Overlay Networks**

    Ports to Open:
    
        TCP port 2377: For cluster management.
        TCP and UDP port 7946: For node communication.
        UDP port 4789: For overlay network traffic.

**Setting Up Overlay Networks**

    Prerequisite: Docker Swarm must be initialized (i.e., you need to either create a new Swarm or join an existing one) before you can create an overlay network.

Summary

    Overlay Network allows containers on different Docker hosts to communicate.
    
    Ingress Network is used for routing traffic to Swarm services.
    
    Bridge Network connects Docker nodes together.
    
    Open specific ports for proper network communication.
    
    Initialize Swarm before creating overlay networks.

**Demo - Networks in Docker Swarm**

Let's Play with dokcer lab - It's comes with no additional cost.

```
https://labs.play-with-docker.com/
```

![image](https://github.com/user-attachments/assets/e2d37c34-9479-450a-80a8-f659d109e14a)

I go with 3 Manager nodes and 2 worker nodes

Connect any manager node

**To create a new network**

```
docker node ls
docker network ls
docker network create -d overlay <network-name>
docker network create -d overlay my_network
docker network ls
```

![image](https://github.com/user-attachments/assets/25b4553d-8d88-408d-8f8d-e98244a72e57)

**To check any services are running in this swarm cluster**

```
docker service ls
```

**To create a postgres service in the cluster**

```
docker service create --name <service-name> --network <network-name> -e POSTGRES_PASSWORD=<password> <postgres-image-name>
docker service create --name postgres --network my_network -e POSTGRES_PASSWORD=mypass postgres
```

![image](https://github.com/user-attachments/assets/724a7bd6-40b7-4fa1-81ba-351d1f0ef07a)

**To create a drupal service in the cluster**

```
docker service create --name <service-name> --network <network-name> -p 8080:80 <image-name>
docker service create --name drupal --network my_network -p 8080:80 drupal
```

![image](https://github.com/user-attachments/assets/e1d4c87b-2e8e-4203-a292-1485e3684893)









