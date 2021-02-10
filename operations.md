# Operating a multi-tenant cluster 

First we need to understand the shared responsibility model in AKS when it comes to operations, please familiarize yourself with the [AKS support policies](https://docs.microsoft.com/en-us/azure/aks/support-policies).

In this section we will outline the different areas of operations and the recommendations.

* workloads operations 
* cluster operations 


## workload Operations

The optimal scenario is that the feature teams should be equipped with enough knowledge and skills to operate their workloads, in terms of deployments, scaling, secret management, and monitoring. 

Now the reality is not all teams are at the same level and if knowledge and skills are required then this means slow onboarding for teams on the new Kubernetes platform, this is where a lot of 
enterprises decide to abstract parts of the cluster to ease the onboarding, such as providing templates for deployments and the other important APIs. this model accelerates onboarding, however, it isn't sustainable if more teams were onboarded, as such there has to be be a skilling plan within the organization for the feature/development teams on docker, Kubernetes, and the cloud platform which in our case is Azure:) 



## Cluster Operations 

Multi-Tenant clusters have to be operated by a central team to ensure smooth and consistent operations. this central team should be able to cover all the required disciplines to operate the 
clusters such as VMs, Networking, Security, Monitoring, CI/CD pipelines, and of course Kubernetes it self as a platform. 

- Identity and Access management remains the most complex topic in the enterprise, as a single AKS cluster will span different services, how easy it is depends on how the central team is being setup, if all the different disciplines are within one team then a single access group could be enough, but if the team is distributed then more granule authorization should take place. 

* Identity and Access management for users is made easy in AKS using the Azure Active Directory integration, [more here](https://docs.microsoft.com/en-us/azure/aks/managed-aad)
* Deployments/Applications identity to authenticate against Azure Cloud Resources i.e. KeyVault can be achieved using the [Pod Identity Project](https://docs.microsoft.com/en-us/azure/aks/use-azure-ad-pod-identity)
* Providing identity for external users to access your clusters is normally applied on the Ingress Controller, the most of the ingress controller implementations has support for the [oauth2-proxy](https://github.com/oauth2-proxy/oauth2-proxy) which provides integration with Azure Active Directory and other authentication repositories, for example [Ambassador](https://www.getambassador.io/docs/latest/howtos/sso/azure/)

- Networking is covered in [Network Section](networkTenancy.md)
- Cluster upgrades is dependent on the [Compute Tenancy Model](computeTenancy.md)
- Security is covered in the [Security Section](security.md)







