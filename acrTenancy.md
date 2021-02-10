# Container Registry Tenancy 

In this section we will discuss how to design Azure Container Registry in order to cater for multi-tenant clusters.

The most common question is should we use a single or multiple registries?
The short answer is multiple, as we need to prevent production container images from any sort of tampering and at the same time we need to provide developers with enough access during the development phase to minimize disruptions on their workflows. 



## What would be the end state?

We will create multiple registries based on the development stage (Dev, Staging, UAT, and Production), below we will delve deep into each stage and what's required. 



### Production Container Registry 

1. **Access** who should access the production registry? 
- *Write Access* Only one system with Push action, which is your Continuous Integration (CI) System
- *Read Access* Only one system with Pull access, which is your AKS cluster(s).

2. **How to prevent images from being overwritten?**
- ACR provides a feature called [lock deployed image tags](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-image-lock) which will lock the image once its pushed to the registry and this action can be taken in the image build flow 

3. **What should we store in the production registry?**
- Base container images (golden images)
- Production workload container images

4. should we use Single or Multiple production registries per tenant?
- As access is pretty tight, normally one would be enough 
- Important consideration is the registry throughput, check it out [here](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-skus
) 


5. **Should we mix base images with Production Workload Images?**
- Base images will require to be pulled from all developers, if you want to keep base images with other Production Workload Images then you have some options not limited to the below 
1. An [ACR Import](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-import-images) task on a schedule to from the pipeline to import base images to Dev Registries 
2. Pull Access base images can be granted to developers using [tokens with repository scoped permissions](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-repository-scoped-permissions)
3. [ACR supports webhooks and eventing](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-webhook), you can use such features to create automation workflows too 

- The same login applies for Production images, as developers will need to pull them in order to do any update. 




### Dev Registry 

Here its recommended to have one per team, with full access to the team to pull and push

### Staging and UAT
Same logic applies as dev registry, in short the container registry should follow your branching strategy 





### Other Considerations

1. *Follow the ACR registry* [best practices](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-best-practices)
2. *Scan images for vulnerabilities* Azure Security Center provides an integration with Azure Container Registry to provide [this feature](https://docs.microsoft.com/en-us/azure/security-center/defender-for-container-registries-introduction?bc=/azure/container-registry/breadcrumb/toc.json&toc=/azure/container-registry/toc.json) or you can use any of third part image scanning tools. 
3. *Test your code* 
4. Use private link for registry to prevent access from outside, more [here](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-private-link)
5. [Label everything](https://rollout.io/blog/container-image-immutability-power-metadata/)
6. Use build promotions (dev, qa, etc..)
7. [You can restrict public access to a registry or disable it as a whole](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-access-selected-networks)


