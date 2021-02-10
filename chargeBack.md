# Charge Back 

Some Enterprises want to implement a charge back mechanism in order to to be able to forecast budget and provide on-demand type resources. 

Multi-tenant clusters introduces a challenge as the charge back needs to be on the namespace level, which as of today isn't supported by Azure Billing, there is a tentative plan between the AKS team and the Azure Billing team to introduce such a feature somewhere in 2021. 

What needs to be done is depending on the compute tenancy model:

1. Dedicated Node Pool per teanant: this one is pretty much straigh forward to implement, we use the same model of billing tags for nodes and we charge based on this. another thing that worth mentioning is that the new [AzureCSI Disk driver offers tagging the storage](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md), thus the 2 biggest items compute and storage are covered with tags.

2. Shared Node Pool: this one is more complex as compute resources are shared as such we can charge on name space utilization level. we have seen customers implementing different models to accomplish this:

* based on monitoring data: name space utilization is checked on a monthly basis and the cetral team decided how much they will charge per compute unit. 
* using some tools in the market, the most popular one being [KubeCost](https://kubecost.com/)
 

 ## Network Traffic Charge Back
You will be charged for the egress traffic from your nodes to internet and across avaialbilty zones, this one would be a bit challengeing to achieve if not using something like Kubecost, as you will have to monitor the egress traffic to inetrnet and between pods and aggregate this over a period of time to provide reliable charge back. 