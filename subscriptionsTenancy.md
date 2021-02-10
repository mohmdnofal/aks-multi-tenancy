# Azure Subscriptions 
Customers have historically used subscriptions as a mean of segregation for workloads, business units, environments, etc... official guidance can be found (here)[https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/decision-guides/subscriptions/].

For multi-tenant clusters, you will find your self with the need for fewer subscriptions as multiple-workloads will be sharing the same cluster, but you can still make use of subscriptions for the below reasons:
1. Subscription per environment: in this model you can split based on environment i.e. dev, staging, UAT, and production. 
2. Subscription per business unit or group of business units: if your business units are big enough you could decide that you provide multi-tenant cluster per business unit or a group of business units. 
3. Of course a hybrid model can be used as well, 
