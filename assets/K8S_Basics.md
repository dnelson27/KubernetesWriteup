# Baby's First Kubernetes
## What is it?
- Kubernetes (K8s) is an open-source container orchestration tool for managing applications on container runtimes like Docker.
- K8s uses a collection of controller services hosted on a __master__ server(s) to manage __worker__ servers in a server cluster. The application code is held on the worker servers, and the master server controls when, what, and how containers are built on the worker servers.
### High-level K8s Architecture

![](assets/simple_kubernetes.png)
- Kubernetes Documentation at: https://kubernetes.io/docs/home/
- Docker Documentation at: https://docs.docker.com/
---
## Why use it?
- K8s is a tool that allows you to centrally manage coontainer image deployment and updates, define resource usage standards, monitor the health of an application, and maintain control over an environment at-scale when running services on containers.
- If configured correctly, provides high fault-tolerance for your container app.
- it is best use where containers are being used for large apps with mutliple microservices requiring High Availibility and redundancy. 
---
## Alternatives
- K8s is NOT the only solution for managing containers at scale, but it is the most popular. Other solutions include Amazon Elastic Container Service (ECS), Docker Swarm, and OpenShift.
- For containerized small apps on a single server,a more straight-foward service with less focus on distributed services like Docker Compose is the best way to go.
- Amazon Elastic Kubernetes Service (EKS) and Google Kubernetes Engine (GKE) are managed solutions that allow teams to delegate the managment of the underlying operating system and hardware to a cloud provider.
---
## A few key SysOps/Deployment Concepts
### High Availibility
- __High Availibility (HA)__ is the concept of making your application/service fault-tolerant and redundant, as to prevent downtime in the case of an incident. Examples of incidents that High Availibility configurations attempt to solve for are:
    - A database has a hardware failure that results in the loss of data held on that server.
    - A natural disaster knocks out power in a datacenter, leaving your servers unresponsive for a period of time.
    - A bug causes an application to crash unexpectedly.

- Common solutions for providing __High Availibility__ include:
    - hosting servers in multiple data centers across multiple regions (ideally, at least 3).
    - backing up data to storage that is isolated from the data store being backed-up.
    - Automate health-checks to fail-over between servers if a server is experiancing issues
    - Disaster planning, have back-up servers on standby

## Scalability
- Providing __Scalability__ in the context of a application is the process of building a system to effeciently adjust for variance in compute or traffic load. When the amount of people shopping for electric drumkits and gamer chairs doubles on cyber monday, an online store and its components should be able to adjust on-the-fly to handle the load
- To allow your application/service to be scalable, you can:
    - Monitor the health/resource usage of application servers and write code to create new virtual machines to meet new requirements. Have your automation delete these new virtual machines when the load is back to normal.
    - Use one of the many solutions provided by cloud providers, like AWS EC2 AutoScaling, to create new cloud servers to match the compute capacity needed, delete these servers when the load decreases.
    - Use Load Balancers to direct traffic to instances using round-robin or other traffic direction method to allow traffic to reach new servers on-the-fly
- Applications can scale __horizontally__ or __vertically__
    - Horizontal scaling: Create new instances of the application server of the same/similar size. Ex: a server uses 90% of its CPU capacity, a new virtual server is created with the same CPU capacity and requests are load-balanced between the two servers.
    - Vertical scaling: Create a larger version of the application server. Ex: a server uses 90% of its CPU capacity, a new instance is created with a higher-end CPU and traffic is redirected.
### Okay, finally back to Kubernetes
## Kubernetes Components
### Key Terms:
- __Node:__ A single virtual/physical server/instance
- __Control Plane__: the server(s)/instance(s) that command and control the worker nodes
- __Pod:__ One or more containers on a node, smallest component in K8s
- __Load Balancer:__ A physical or virtual networking component that directs traffic between nodes.
### Nodes
- Nodes are individual physical/virtual servers that contain a number of components to send/recieve instructions from the master node (see __Control Plane__ for details) and host the containers running your service. Node components include:
    - __Pod:__ the smallest unit in the K8s architecture, pods contain one or more containers that are tightly coupled to serve a single function. Multiple container pods might be implemented where a seperate container is acting as a networking proxy, acting as a helper or sidecar container to handle logging, monitoring, 
    - __kubelet:__ the K8s agent running on each node that communicates back to the control plane
    - __kube-proxy__: A network proxy that maintains network rules permitting traffic from the node to other parts of the cluster, or outside of the cluster.
### Control Plane
- The Control Plane is a collection of cluster managment services that run across multiple servers. These services track the health of worker nodes, keep inventory of what containers are deployed and where, track and update what container images are being used, and provide an API interface for administrators to manage the cluster.
- Some Control Plane Components Are:
    - __API Server:__
        - Exposes the K8s API, usually with the kube-apiserver implementation. This implementation scales to match load needs by deploying new instances to handle API requests (horizontal scaling)
    - __Cloud Controller Manager (optional if running locally):__
        - Controls the cloud infrastructure for your specific cloud provider's infrastructure.
        - Service Controller: Creates load balancers
        - Route Controller: manages routes within the cloud provider's infrastructure
        - Node controller: Checks to see if an unresponsive node was terminated by the cloud provider
    - __Controller Manager:__ kube-controller-manager runs each of the controller processes as a single binary. These controller processes include:
        - Node controller: Checks on the status of nodes and respond when a node goes down
        - Replication controller: maintains the correct number of pods.
        - Endpoints controller: joins services to pods by populating an Endpoints object
        - Service accounta nd Token controllers; Creates the default accounts and API access tokens for new namespaces
    - __Scheduler:__ kube-scheduler watches for newly created pods that have not been assigned a node, and selects a node for them to run on based on resource requiremetnts, hardware/software policy constraints, label-based eligibility (affinity and anti-affinity), deadlines and workload interference.
    - __etcd:__ A highly-availible (distributed across servers) key-value store for all persistent cluster data. Periodic back-ups are recommended here.

## Simple Example
The following use minikube, a stripped down, single node version of kubernetes to deploy a simple application that will send/recieve data from a frontend NGINX server to a backend Flask server
1.  Install minikube by following: https://minikube.sigs.k8s.io/docs/start/
2. Install a container or VM manager like Docker or VirtualBox, compatible "drivers" are availible here https://minikube.sigs.k8s.io/docs/drivers/
3. Start minikube with:
````
minikube start
````


## In Practice
- Kubernetes at-scale can require a significant amount of engineering resources and can become complex as an application grows and the number of microservices increases. Running self-managed K8s in production should be restricted to companies with experienced staff and the people-power to manage it securely.
- If a team runs a service or application running across multiple cloud providers, A service like Amazon's Elastic Kubernetes Service (EKS) may be a better alternative. EKS provides high availibility and redundancy by default, with three master nodes across three availibility zones (data centers) by default, but can still be controlled via the K8s api using kube-ctl.
- If a team runs an AWS-native application, a managed service like Amazon ECS on EC2 or Fargate can be employed to more easily integrate with AWS-native services.