** **

## HPC Scheduling on Kubernetes with KubeFlux

** **

## 1.   Vision and Goals Of The Project:

Kubernetes provides an open source platform to deploy, scale and manage container based applications. High performance computing (HPC) and cloud technologies are increasingly coupled to accelerate the convergence of traditional HPC with new simulation, data analysis, machine-learning, and artificial intelligence approaches. While the HPC and cloud paradigm is increasingly being adopted, several key mismatches between HPC and cloud technologies still preclude this paradigm from realizing its full potential.

Kubernetes's default scheduler, initially intended for microservice with limited requirements, is now facing some issues in handling huge HPC load. IBM Research, Lawrence Livermore National Laboratory, and Red Hat share a common vision to bring complete HPC scheduling awareness into Kubernetes. KubeFlux, a scheduler plug-in for Kubernetes that uses Flux's advanced graph-based scheduler, Fluxion as the module taking pod placements decision. There are various aspects of workload management that needs KubeFlux attention like co-scheduling, job throughput, coordination, portability and extremely heterogeneous resources. 

In this project, we are looking at the Job coordination aspect of KubeFlux. Our goal is to build a controller that can manage job cancellation at KubeFlux level when pods are completed/terminated, so that KubeFlux internal status reflects the actual state of the cluster. KubeFlux also needs a method that can dynamically update its internal status when sharing resources between Kubernetes and KubeFlux, when jobs are deployed by Kubernetes itself or other schedulers on the same shared resources. Overall our vision is to enhance KubeFlux so that it provides smooth job management service. 

## 2. Users/Personas Of The Project:

This project is open source. The users range from Academic researchers to Companies who want to run containerized performance sensitive workloads on Kubernetes/OpenShift platforms. Through this project we intend to keep the process of job scheduling as efficient as possible along with reducing the overhead of a scheduler. This way we continue to provide Kubernetes’s audience this as a feature along with the plugin KubeFlux.

** **

## 3.   Scope and Features Of The Project:

There are several aspects to be addressed when using third-party software to take scheduling decisions in a Kubernetes cluster. 

A very important one is state management due to resource sharing between Kubernetes' scheduler and the plug-in scheduler: Fluxion has its own internal state and resource management, and Fluxion’s internal state of the cluster needs to be updated any time new pods created by other schedulers and when they are deleted. Features of the project include supporting a controlled but dynamic approach towards sharing the resources with KubeFlux and scheduling jobs for Kubernetes pods.  
KubeFlux will be released as part of 4.10+ OpenShift release in 2022.


** **

## 4. Solution Concept

![High-Level-Representation](https://github.com/JuhiPaliwal/CS6220-HPC-Scheduling-on-Kubernetes-with-KubeFlux/blob/main/HighLevelArchitecture.png)

- Kubernetes:  An open-source container-orchestration system for automating computer application deployment, scaling, and management.
- Flux: A framework that acts as a job scheduler and resource manager that is implemented to schedule batch jobs on supercomputers. A supercomputer could have a complex system and complex workloads. Flux is kind of a Kubernetes for HPC systems.
- Fluxion: The core scheduler of the entire Flux framework.
- Scheduling Framework: A pluggable architecture for Kubernetes allowing the implementation of new schedulers through a set of plug-in API extension points.



Fluxion combines graph-based resource modeling with efficient temporal plan management schemes to schedule a wide range of HPC resources. It adds queuing and resource matching functions to the old version. The project aims at the scheduling resource service module. This module uses graphs to represent the resources and their relationship. Kube-Scheduler’s selects a node in the pool of worker nodes for the pod and then notifies the API server about this decision. Kubernetes has a view on using these nodes whereas KubeFlux has its own implementation. Kubernetes knows when a pod is scheduled through Flux but, when a pod is completed, Kubernetes will remove it from the cluster, except this information is not propagated to KubeFlux. Thus we need to introduce an informer that looks for completed pods and informs KubeFlux that its scheduled pods are done.

## 5. Acceptance criteria

- Minimum acceptance criteria:
    - Implementing a controller for job cancellation management (for pods allocated by KubeFlux).
    - The informer component of the controller shall inform KubeFlux of state changes for its allocated pods in the cluster.
    - KubeFlux shall have an updated view of the state of its allocated pods either when said state is changed or when KubeFlux needs to make a scheduling decision.

- Stretch goal:
    - Proposing methodologies to dynamically update Fluxion's internal graph of resources to address resource sharing between Kubernetes and Fluxion (for pods allocated by other    schedulers).
    - The system shall provide an interface for adding resource sharing polices.


## 6.  Release Planning:

• Release 1
    o Learn about Kubernetes and Fluxion
        - Read about Kubernetes architecture.
        - Focus on the functionalities of Default Scheduler in Kubernetes.
        - Understand the graph-based implementation of Flux Scheduler.
    o Building the Environment
        - Install Kubernetes for different OS.
        - Install dependencies like Helm Chart, Minikube etc.

• Release 2
    o Analysis of the underlying resource graph used by Flux to represent the Kubernetes cluster
        - Getting familiar with Golang.
        - Read Resource-query documentation.
    o Designed Pi program test and reproduced the state inconsistency issue in practice
        - Setting up Kind and Local Registry.
        - Run PI program with Default Scheduler.
        - Run PI program with KubeFlux Scheduler.

• Release 3
    o Codewalk by our mentors
        - Discus possible solutions for handling state inconsistency problem.
    o Develop the informer
        - Log file for each POD.
        - Handle more PodPhases in updatePOD Function.
        - Add PodInformer (for successful PODs).
        - Save a set of Job IDs in scheduler object.
        - Add NodInformer.
        - Create corner test cases.

• Release 4
    o Create an operator through operator-sdk
        - Create CR based on POD information.
        - Delete CR after POD completion.
        - Fetch POD and read status in reconciliation loop.
        - Add experimental code in reconciliation loop.
    o Define CustomResourceDefinition (CRD).
    o Test CRD with YAML file manually.

• Release 5
    o Demo informer in an open shift cluster (real world environment).
    o Performance analysis of the Pod Informer.
