# Monitoring and Logging

## Monitoring
Monitoring has two parts, infrastructure and applications, here we will focus on the infrastructure monitoring only.

AKS has a monitoring add-on using (azure monitor for containers)[https://docs.microsoft.com/en-us/azure/azure-monitor/insights/container-insights-overview], this out of the box provides great visibility on the infrastructure and Kubernetes components. however, it doesn't provide RBAC per namespace, as such all logs and metrics can be granted access, whether read or write to the whole of workspace. In some case customers don't want to share monitoring data across teams for compliance reasons even if its in read only mode, as a result of this there is a need to look for alternatives. 

Before delving into the alternatives, the central team managing the cluster should make use of the azure monitor for containers as its the fastest and easiest way to get insights for what's happening inside the cluster, and it can co-exist with other monitoring systems. 


The most popular option for monitoring Kubernetes clusters is Prometheus and Grafana for dash-boarding, both together support RBAC per namespace on the monitoring dashboards and wildly used by customers. there are of course plenty of other commercial solutions which can deliver the same functionality. 

## Logging
As for logging the same goes, Azure Log Analytics doesn't provide RBAC per workspace, an alternative would be something like Elastic Search or(Loki)[https://github.com/grafana/loki]

If you want to provide RBAC based on Dashboard that is possible today by using the following guide [https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboard-share-access]
