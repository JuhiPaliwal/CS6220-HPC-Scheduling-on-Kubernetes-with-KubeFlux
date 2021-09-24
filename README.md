** **

## HPC Scheduling on Kubernetes with KubeFlux

** **

## 1.   Vision and Goals Of The Project:

Kubernetes provides an open source platform to deploy, scale and manage container based applications. High performance computing (HPC) and cloud technologies are increasingly coupled to accelerate the convergence of traditional HPC with new simulation, data analysis, machine-learning, and artificial intelligence approaches. While the HPC and cloud paradigm is increasingly being adopted, several key mismatches between HPC and cloud technologies still preclude this paradigm from realizing its full potential.

Kubernetes's default scheduler, initially intended for microservice with limited requirements, the scheduler is now facing some issues in handling huge HPC load. Default schedulers need the job to be broken down into their respective running containers in a cluster (pods) before being scheduled which increases the overhead. IBM Research, Lawrence Livermore National Laboratory, and Red Hat share a common vision to bring complete HPC scheduling awareness into Kubernetes. KubeFlux, a scheduler plug-in for Kubernetes that uses Flux's advanced graph-based scheduler, Fluxion as the module taking pod placements decision. There are various aspects of workload management that need KubeFlux attention like co-scheduling, job throughput, coordination, portability and extremely heterogeneous resources. 

In this project, we are looking at the Job coordination aspect of KubeFlux. Our goal is to build a controller that can manage job cancellation at KubeFlux level when pods are completed/terminated, so that KubeFlux internal status reflects the actual state of the cluster. KubeFlux also needs a method that can dynamically update its internal status when sharing resources between Kubernetes and KubeFlux, when jobs are deployed by Kubernetes itself or other schedulers on the same shared resources. Overall our vision is to enhance KubeFlux so that it provides smooth job management service. 

## 2. Users/Personas Of The Project:

This project is open source. The users range from Academic researchers to Companies who want to run containerized performance sensitive workloads on Kubernetes/OpenShift platforms. Through this project we intend to keep the process of job scheduling  as efficient as possible along with reducing the overhead of a scheduler. This way we continue to provide Kubernetes’s audience this as a feature along with the plugin KubeFlux.

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
Scheduling Framework: a pluggable architecture for Kubernetes allowing the implementation of new schedulers through a set of plug-in API extension points.



Fluxion combines graph-based resource modeling with efficient temporal plan management schemes to schedule a wide range of HPC resources. It adds queuing and resource matching functions to the old version. The project aims at scheduling the resource service module. This module uses graphgraphs to represent the resources and their relationship. Kube-Scheduler’s default scheduler selects a worker machine in the control plane(node) for the pod and then notifies the server about this decision. Kubernetes has a view on using these nodes whereas Kubeflux has its own implementation. Kubernetes knows when a pod is scheduled through flux but when said pod is done Kubernetes will remove it from the cluster, except this information is not propagated to flux. Thus we need to introduce an informer that looks for completed pods and informs flux that its scheduled pods are done.

## 5. Acceptance criteria

There are two main tasks to be fulfilled:
1. Implementing a controller for job cancellation management (for pods allocated by KubeFlux)
2. Proposing methodologies to dynamically update Flux internal graph of resources to address resource sharing between Kubernetes and Flux (for pods allocated by other schedulers). Note, this is a stretch goal.


## 6.  Release Planning:

- Installing and Running the current KubeFlux Plugin.
- Implementation of a controller with an informer on pod updates.
- Implementation of the entry points in KubeFlux to allow job updates and resource graph updateupdates.
- Testing on OpenShift with GROMACS benchmarkk.
- Proposing a method to update Flux resource graph with the resources shared  between kubernetes. (stretch goal)


** **

## General comments

Remember that you can always add features at the end of the semester, but you can't go back in time and gain back time you spent on features that you couldn't complete.

** **

For more help on markdown, see
https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

In particular, you can add images like this (clone the repository to see details):

![alt text](https://github.com/BU-NU-CLOUD-SP18/sample-project/raw/master/cloud.png "Hover text")

