# Security and Multi Tenancy

Security in multi-tenant clusters require good level of experience from the operations team in order to ensure safe co-existance between tenants, while all of the above should apply in dedicated tenancy clusters too, but in multi-tenant clusters 



## Authentication and Authorization 

We have different areas here, we need to provide identities for admins who are operating the clusters, or developers deploying their applications to the cluster. we need to provide identities for our deployment to authenticate against Azure resources. We also need to provide identities for users accessing our applications. and finally we need to provide identities for CD pipelines so they can deploy to the cluster. all of these identities require Authorization to interact with the cluster resources or the applications. 


* Identity and Access management for Users is made easy in AKS using the Azure Active Directory integration, [more here](https://docs.microsoft.com/en-us/azure/aks/managed-aad)
* Deployments/Applications identity to authenticate against Azure Cloud Resources i.e. KeyVault can be achieved using the [Pod Identity Project](https://docs.microsoft.com/en-us/azure/aks/use-azure-ad-pod-identity)
* Providing identity for external users to access your clusters is normally applied on the Ingress Controller, the most of the ingress controller implementations has support for the [oauth2-proxy](https://github.com/oauth2-proxy/oauth2-proxy) which provides integration with Azure Active Directory and other authentication repositories, for example [Ambassador](https://www.getambassador.io/docs/latest/howtos/sso/azure/)
* Identities for continuous delivery pipelines, here you have 2 options:
  * the most wildly used option is service accounts, for example [here is how its done in Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#sep-kuberhttps://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#sep-kuber)
  * [Kubelogin](https://github.com/Azure/kubelogin) can be used to provide non-interactive login for pipelines where the identity is in Azure Active Directory. 


## Policies 

We need to ensure that our containers comply with our organization policies such for example we can't run privileged containers or you can only pull images from specific container registries for this the need for policy engine is required.

[Azure Policy for AKS](https://docs.microsoft.com/en-us/azure/aks/use-pod-security-on-azure-policy) can help accomplish such tasks. Needless to say that there are other open source tools and commercial offering which can help deploy policies to your clusters as well. 

## Managing secrets 
[Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/https://kubernetes.io/docs/concepts/configuration/secret/) comes with their [Risks](https://kubernetes.io/docs/concepts/configuration/secret/#risks) to avoid any of these risks its advised to rely on an external secret repository which will allow us to store our secrets securely and allow to mount/inject secrets during run time only.

Azure has an implementation which helps to accomplish this in combination with Azure Key Vault, the project is called [Secret Store CSI Driver](https://kubernetes.io/docs/concepts/configuration/secret/#risks).


## Runtime security 

We need to ensure that all activities in the containers are captured and analyzed and same goes for the host. 

Azure Security Center has developed a solution called [Azure Defender for Kubernetes](https://docs.microsoft.com/en-us/azure/security-center/defender-for-kubernetes-introduction) which provides run time security and container scanning.

Also there great partner solutions with advanced functionality which can help achieve a great security posture for your clusters such as Aqua, Twist Lock, Stack Rox, and Nuvector to name a few. such solutions provide a suit of features which include Policies, Pipeline integrations, Network Policies, Run Time Security, so basically they act as one stop shop for security.


