## Dedicated vs Multi Tenancy Clusters  

Here we will do a high level comparison between the 2 models;

1. Ownership and Operations

*Note* : Workloads or Applications are in all cases the under the ownership of the tenant 
**Dedicated Tenancy**

In this model each tenant has its own set of clusters, which depending on their experience can take full ownership of or delegate the management to a central team. this model on the long run will prove difficult to maintain as the number of clusters grow. 

**Multi Tenancy**

In this model there has to be a central team taking care of the clusters as the tenants are assigned virtual clusters, depending on the [Compute Tenancy Models](computeTenancy) the operations load could vary. 

2. Onboarding

**Dedicated Tenancy**

Depending on the ownership model, if the tenants are expected to own and operate their clusters then onboarding new teams could be slow as there has to be a learning curve for the owner team to get comfortable operating Kubernetes clusters. 

**Multi Tenancy**

Faster onboarding for new teams, as the learning curve is mostly related to development which is common between the 2 models.

3. Security

**Dedicated Tenancy**

Provides for free isolation, as each tenant has the workloads it owns within its clusters, normal practices for permitter security can be enough at the beginning and teams can add more Kubernetes security controls as they grow in experience 

**Multi Tenancy**

Requires experienced teams to achieve the desired level of security, as tenants are sharing the underlying infrastructure. 

4. Cost 

**Dedicated Tenancy**

High in terms of cost, as there will be a lot of duplication and idle resources, and not all teams have experience in optimization. 

**Multi Tenancy**

Optimized cost, the central team has visibility on all clusters and they normally have experience in optimization. 


5. Resiliency  

**Dedicated Tenancy**

Varies depending on the owner team experience

**Multi Tenancy**

Central team normally ensures decent level of resiliency across all workloads, and can handle exceptions. 

