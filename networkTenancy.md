# Networking and Multi Tenancy

In this section we will discuss the below topics

1. Which network plugin should I use? 
2. How to achieve network isolation?
3. Ingress setup 
4. Private and Public Clusters and Services

## Which network plugin should I use? 

AKS offers 2 network plugins Azure CNI and Kubenet, the differences are thourougly covered in this [document](https://docs.microsoft.com/en-us/azure/aks/concepts-network).

When it comes to multi-tenancy both options work and will deliver the same outcomes, however, in the spirit of completeness below is an easy criteria to decide which one to use: 

1. If you don't have any shortage with IPv4 allocation, use Azure CNI, it offers an out of the box experience and pretty easy to manage. 
2. If the workload isn't performance sensitive in terms of throughput, i.e. the pod will be moving large data sets to external services like a database. Use Azure CNI as it offers VM like performance. While Kubenet offers great performance, you won't be getting the a VM like network performance from your pods. 
3. If you have problems with IP allocation i.e. network team only provides small subnets, then Kubenet would be ideal solution as the pods will be assigned non-routable IP space. 

*Does the Network Plugin impact the Hub and Spoke model we have internally?*

Most enterprises have [hub and spoke model](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) network model  where the Hub has central network services and other shared services and the spokes represent the workloads in your case AKS clusters and its surrounding services.  both Azure CNI and Kubenet support this model, with a bit of technical differences in terms of implementation [highlighted here](https://docs.microsoft.com/en-us/azure/aks/egress-outboundtype)

AKS Baseline Architecture provides general [guidelines for designing the network](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Faks%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json#network-topology) for AKS clusters, while its not specific for multi-tenancy, still it mostly applies regardless of the tenancy model. 


## How to achieve network isolation?

AKS supports 2 network policies Calico and Azure CNI Network Policy [highlighted here](https://docs.microsoft.com/en-us/azure/aks/use-network-policies)

**Network Policies** are a **must have** to provide isolation for the pods network traffic within and across namespaces, in a multi-tenant cluster its an absolute must to have Network policy enabled and enforced. 

*Typical question we receive from customers when it comes to network policies is who should apply these policies the workload owner or the network/security team?* 

While the optimal scenario would be to trust the workload owner to deploy their network policies, the reality is a bit different, most enterprises have dedicated network teams who would like 
to have visibility and control on every packets ingress-ing or egress-ing their network. as such a good practice is to designate [RBAC roles](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) for the network team where they can apply or modify network policies. 

Another way to make applying all the rules needed to secure workloads more easier is the using gitops, which will be discussed in the [deployment section](workloadDeployments).


## Ingress Setup

Normally any Kubernetes cluster will make use on an Ingress Controller of sort, and there are many options in the market, we won't be discussing which one is best but we will cover the 
different deployment options to achieve safe multi-tenancy.

1. **Single Shared Ingress** across all tenants, in this model one ingress controller "deployment" will be used to serve all tenants. important things to consider

* **Use dedicated node pool for ingress controller**, ingress controller is normally one of the most heavily used resources in your cluster, dedicating a node pool for the ingress is a good practice to achieve good level of availability and scaling, consider this guidance for any shared system tool that you will be using i.e. Monitoring. 
* **Ensure proper scaling is taking place for your ingress controller** 
* **If charge back is a requirement** then the easiest option is to charge a flat rate per tenant for the shared ingress. 

2. **Dedicated ingress controller per tenant** in this model each tenant will get its own ingress controller dedicated to its workloads 
* The ingress controller can live in the tenant namespace
* The ingress controller can be deployed in the tenant node pool assuming node pools is your tenancy model of choice 
* You need to make use of the [class object](https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-class) in order to ensure traffic is handled by the correct ingress 
* You can still deploy all the different ingresses in one dedicated node pool similar to the previous model 






## Private and Public Clusters and Services

### Clusters 
By private or public cluster we refer to the API server being assigned either a public IP which is the default behavior in AKS or Private IP using [private link in AKS](https://docs.microsoft.com/en-us/azure/aks/private-clusters) in both cases worker nodes are always assigned Private IP address.

In relation to multi-tenancy the choice won't make much difference, the only difference is that with a multi-tenant cluster you will have many personnel authorized to access the API server 
so the surface is a bit large, but then even in public API server you can still use [authorized ip ranges](https://docs.microsoft.com/en-us/azure/aks/api-server-authorized-ip-ranges) to ensure access is only grated to your offices or VPN IPs. 

The choice of Public or Private Clusters usually comes down to compliance and governance requirements, some customers prefer no public IP anywhere and private clusters was introduced for this reason. 


### Services 
*How about if a tenant came with the requirement to make a service accessible over Internet?*

In this case its always preferred to use only private IPs for your services and the services required to be exposed to the public can be fronted by a WAF outside your resource group to handle such requests. 





## Verdict 

As outlined the different networking options won't really change much on the outcome, the 2 areas where customers spend time is deciding on the roles i.e. who to apply network policy, and a gitops model in this case will make things much easier.

The other area, is ingress and the preference is always to keep the ingress isolated in its own node pool.


