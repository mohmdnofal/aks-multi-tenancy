# Kubecost on Azure Kubernetes Service (AKS)

The tutorial is available [here](https://kubecost.com/install).

I'll be using helm3 for the installation 
```shell
$ helm version
version.BuildInfo{Version:"v3.4.1", GitCommit:"c4e74854886b2efe3321e185578e6db9be0a6e29", GitTreeState:"dirty", GoVersion:"go1.15.4"}
```


```shell
kubectl create namespace kubecost
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm install kubecost kubecost/cost-analyzer --namespace kubecost --set kubecostToken="bW9obWQubm9mYWxAZ21haWwuY29txm343yadf98" --set networkCosts.enabled true 
```


```shell
#test by portforwarding 
kubectl port-forward --namespace kubecost deployment/kubecost-cost-analyzer 9090

#open your browser and head to http://localhost:9090, this could take up to 10 mnts to load properly 

```


**Update Kubecost**
```shell
helm repo update && helm upgrade kubecost kubecost/cost-analyzer -n kubecost --set networkCosts.enabled=true 
```


**Delete Kubecost**
```shell
helm uninstall kubecost -n kubecost file_copy
```


