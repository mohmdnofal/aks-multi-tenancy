# Multi Tenancy in Azure Kubernetes Service

This document is scoped for enterprises operations teams trying to build and deliver a managed Kubernetes platform on top of Azure Kubernetes Service to the different development/feature teams internally.

The main purpose for this document is to serve as a guide for how to plan for multi-tenancy in your organization, its based on our collective knowledge helping customers operating Kubernetes clusters on Azure for the past years. The document can't be considered complete and definitely contributions are welcomed. 

*Note* SaaS multi-tenancy isn't the main purpose of this document, but SaaS providers can make use of everything inside this document as its considered a subset of what should be done when there is zero-trust between the tenants. 


### So what's a tenant? 

Kubernetes [multi-tenancy sig](https://github.com/kubernetes-sigs/multi-tenancy) which is a great SIG to join in case you want to delve deeper into Kubernetes multi-tenancy has come up with a definition for a tenant as below;

**A Tenant**  is defined as a team of users/identities that shall have exclusive use of one or more resources of a Kubernetes cluster in parallel with users of other tenants using their own separate and isolated resources within the same cluster.



## Tenancy Models 

There are 2 tenancy models for Kubernetes clusters in the enterprises today
1. Dedicated Tenancy Clusters
2. Multi-Tenancy Clusters 


### Dedicated Tenancy Clusters

In this model each tenant is granted its own isolated cluster(s) which is/are not shared with any other tenant. This model has been the default choice for most enterprises couple of years ago as multi-tenancy in Kubernetes wasn't yet a mature concept, and a dedicated cluster provided enterprises with what so called isolation for free as they didn't have to worry much about the internals of Kubernetes when it comes to tenancy. 

In this model each tenant was granted access to some services (not limited to the below)

1. AKS cluster(s) can be many and distributed across the different stages (Dev, Test, Staging, UAT, Production...)
2. Azure Subscription(s) can be multiple subscriptions depending on the stage for example dev and production or the diversity of the tenant.
3. Virtual Network 
4. The required identities 
5. The container registry(s)
6. The devops tooling (repos, pipelines, boards, etc..)
7. Access to backend i.e. Database (this can be dedicated or shared with governed access)
8. Monitoring Infrastructure 
9. Logging for Infrastructure 
10. Other supporting services for the workload to function properly i.e. A Queue, Functions in Azure Functions, etc...

In this model typically the tenant owner team is responsible for the operations of their environment, namely the AKS clusters, container registries and pipelines as long as it complies with existing governance model in the enterprise.

AKS Best Practices [docs](https://docs.microsoft.com/en-us/azure/aks/best-practices) is a great place to find a baseline architecture for AKS and Kubernetes best practices. 



### Multi-Tenancy Clusters 

In this model tenants will co-exist in a cluster and they will be provided with an isolated virtual environment using Kubernetes Namespaces. This model has been adopted early by some of the experienced customers who had skills operating Kubernetes clusters, but this wasn't the case for the rest. as customers have built a lot of experience in Kubernetes in the past couple of years and taking into consideration all the advancements that happened in Kubernetes, in AKS, and the surrounding ecosystem, multi-tenant clusters became a sought after model for the enterprises. On the other hand the enterprises who went the dedicated cluster path have started to face the challenge of operating 100s of clusters which isn't an easy task and they are now in front of 2 options either to acquire a tool that deliver multi-cluster management which is still a maturing market, or move to multi-tenant model where they can consolidate more and manage fewer clusters. 

The rest of this document is dedicated towards explaining the different options and considerations to help you design a resilient, scalable, and operable multi-tenant Kubernetes platform on top of Azure Kubernetes Service. 


#### Content 
* [High level comparison between Dedicated and Multi tenancy clusters](dedicatedVsMultiTenancy)
* [Compute Tenancy Models](computeTenancy)
* [Network Tenancy Models](networkTenancy)
* [Security in multi-tenant clusters](security)
* [Container Registry Tenancy Model](acrTenancy)
* [Monitoring Tenancy Models](monitoringTenancy)
* [Storage Tenancy Models](storageTenancy)
* [Operating Multi-Tenant clusters](operations)
* [Azure Subscriptions and Tenancy](subscriptionsTenancy)
* [Workload Deployments in Multi-Tenants Environment](workloadDeployments)
* [Billing and Charge Back for multi-tenant clusters](chargeBack)
* [Advancements in the ecosystem towards better multi-tenancy](others)



