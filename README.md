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


