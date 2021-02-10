# Compute Tenancy

In this section we will highlight the different options you have to achieve multi-tenancy on the compute level for your AKS clusters, we will highlight the differences and the recommendations. 


## Compute Tenancy Models

One of the main requirements for tenancy is the ability to isolate compute resources to prevent pods from contending for them. AKS provides the concept of [Node Pools](https://docs.microsoft.com/en-us/azure/aks/use-multiple-node-pools) which we will use as a base compute unit when referencing the different models

1. **Single Shared Node Pool** in this model a group of tenants will share a single node pool, each tenant will be assigned its own Namespace(s), the Namespace(s) will be assigned resources to grant isolation 

2. **Dedicated Node Pool(s) per Tenant** in this model each tenant will get its own node pool(s) in which they will deploy their resources, the tenants will also be assigned Namespace(s) and resource quotas to grant full isolation 




## Common between the 2 models 

Kubernetes Namespace will be the base logical isolation boundary.

Kubernetes out of the box provides great set of APIs to isolate the compute resources, below is the most important ones that should be used irrespective of your compute tenancy model:

1. [Resource Quotas and limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) helps with dedicating compute resources per namespaces and pods in order to eliminate resources contention and it helps with providing efficient scaling. 

2. [Pod Disruption Budgets](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)  Helps to protect resources during upgrade operations and scale down operations 

3. [Pod Priority and Preemption](https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/) Helps in deciding workload priority in terms of scheduling

4. [Node Selector, Labels, and Taints](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector) Helps to protect the system node pool and have dedicated node pool for workloads 

**AKS Specific**
AKS provides the concept of [system node pool](https://docs.microsoft.com/en-us/azure/aks/use-system-pools), which is a great way to isolate system resources from workloads, in both tenancy models System Node pools are a must, in other words, you will always have a minimum of 2 node pools, one for system and another for users. 



## Differences Between the Compute Models 


1. **Cost Optimization**

*Single Shared Node Pool*  

This model provides optimal cost optimization, as all tenants will co-exist in a minimal number of node pools.  While autoscaling for both pods and nodes help with keeping cost at minimal, there is always a need for minimal number of nodes needed to operate the workloads, which in a single shared node pool case this minimal number can be shared across all tenants as such keeps the node utilization optimal.

*Dedicated Node Pool(s) per Tenant*

This model is also optimized, the difference here is that you will have minimal number of nodes per tenant and in this case its not shared so technically there will be more minimum number of nodes than in the shared case.

AKS provides means to minimize the cost on the above scenario
- Scale nodepool to zero in case no workloads are scheduled, more [here](https://docs.microsoft.com/en-us/azure/aks/scale-cluster#:~:text=To%20scale%20a%20user%20pool,0%20as%20your%20node%20count.)
- Use Cluster Autoscaler to automatically scale in/out and to zero your node pools, more [here](https://docs.microsoft.com/en-us/azure/aks/cluster-autoscaler)
- Star/Stop Clusters in case cluster was idle, more [here](https://docs.microsoft.com/en-us/azure/aks/start-stop-cluster)



*which one should I use?*
If cost is the main driver, then Single Shared Node Pool is the most optimized, if you will weight other parameters i.e. operations next to cost, then Dedicated Node Pool model will be the recommended choice.

2. **Ownership** 

In both models a central team is required to operate the cluster(s) infrastructure for smooth operations, however, in dedicated node pools per tenant case, a node pool can be owned by the tenant admin but close collaboration between the tenant admin and the central ops team is required in order to cater for some operations like upgrades, for example central team updates the control plane then informs the tenant admin to upgrade their node pool .

*which one should I use?*
Dedicated node pool per tenant offers more flexibility to the central team where they can offload some of the cluster management tasks to the tenant admin(s), while in single shared node pool, the central team will own infrastructure operations end to end.



3. **Operations** 

One of the most important operations in a k8s cluster is the upgrade of minor versions as it can be considered a breaking operation assuming there were backward compatibility issues in the new version.

*Single Shared Node Pool*  

The central team needs to coordinate with all teams at once for the upgrade, as we have seen before not all teams or workloads are able to keep up with the Kubernetes velocity in 
terms of life cycle management, so some teams can block the upgrade which can impact other teams.

*Dedicated Node Pool(s) per Tenant*
In this case the central team is allowed more flexibility on mixing the node pool versions on the cluster so long its not above the control plane version. 
Kubernetes API allows N-2 on the node pools, as such a 1.19 control plane can support 1.19,1.18, and 1.17 node pools. 

Some good news here is that the community has changed the number ofminor version releases per year to 3 which gives the minor version a support cycle of 1 year. 

Given all that, the central team can upgrade those tenants who are willing to be upgraded faster and give a bit of flexibility to the others who require more time. 

*which one should I use?*
From an operational perspective, Dedicated Node Pool per tenant offers more flexibility to the central team.


To better understand the level of operations required when running AKS clusters please check the below
- AKS [Support Policies](https://docs.microsoft.com/en-us/azure/aks/support-policies)
- AKS [upgrade process and features](https://docs.microsoft.com/en-us/azure/aks/upgrade-cluster)

4. **Sizing**

*Single Shared Node Pool*  

Finding the right node size per node pool would be tricky as multiple tenants with different requirements will be sharing the same node pool, once a new tenant comes with higher 
pod resources requirements than the existing nodes, the need to upgrade to a new node pool will arise in order to accommodate. all existing workloads can be migrated to this node pool, or it can be dedicated to the new tenant to end up in a hybrid approach. 

*Dedicated Node Pool(s) per Tenant*
Here each tenant can get its right sized node pool, and later change on the requirement will impact a single tenant only. 

**Note** factoring Availability Zones here, the same logic goes, some tenants will prefer multi-zone node pools and some won't. 

*which one should I use?*

Clearly Dedicated Node Pool per tenant is the option which will offer the highest flexibility. 


Check the AKS Baseline Architecture document which provides [guidelines for sizing](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Faks%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json#configure-compute-for-the-base-cluster)



5. **Networking**

*Single Shared Node Pool*  
A node pool can exist in a single subnet only, network isolation can be achieved using network policies

*Dedicated Node Pool(s) per Tenant*

As each tenant will live in its own node pool, node pools can either be all in the same subnet or to further isolate they can exist in their own subnets. this scenario can unlock cases where you need to whitelist access on an external network virtual appliance which originates from specific subnet. 

Another [feature](https://github.com/Azure/AKS/issues/1788) in progress which is in particular useful for the customers who chose Azure CNI as the network plugin, where you will be allowed to choose a different subnet for the Pods comparing to the nodes so long they all exist within the same vnet. 


*which one should I use?*
If isolation on the subnet level is a requirement, then Dedicated Node Pools is the clear choice here, other than that both options support Network Policies which in most cases is enough.  


6. **Security**

Both options are equal in here, with a bit of an advantage for the node pool model where you can Isolate on the subnet level too. 



