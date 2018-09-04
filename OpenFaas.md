### OpenFaas
kubernetes run起来之后<br>

Clone the official faas-netes repo from github, which is the Kubernetes backend for OpenFaaS.
```
$ git clone https://github.com/openfaas/faas-netes 
$ cd faas-netes
```
#### Install the Helm on the local machine
```
$ brew install kubernetes-helm
```
You will find the tiller by run this cmd
```
$ kubectl get pods -n kube-system
```

Create RBAC permissions for Tiller
```
$ kubectl -n kube-system create sa tiller && kubectl create clusterrolebinding tiller \
  --clusterrole cluster-admin --serviceaccount=kube-system:tiller
```
Install the server-side Tiller component on your cluster
```
$ helm init --skip-refresh --upgrade --service-account tiller
```
#### Delpoy the Openfaas
Create two namespace. Make sure there are no namespace which name is openfaas and openfaas-fn<br>
```
$ kubectl get ns
  NAME          STATUS    AGE
  default       Active    39m
  kube-public   Active    39m
  kube-system   Active    39m
```
```
$ kubectl create ns openfaas
$ kubectl create ns openfaas-fn
```
Add the OpenFaaS helm chart:
```
$ helm repo add openfaas https://openfaas.github.io/faas-netes/
"openfaas" has been added to your repositories
```
Now deploy OpenFaaS from the helm chart repo:
```
$ helm upgrade --install --namespace openfaas --set functionNamespace=openfaas-fn --set async=true --set rbac=true --set       serviceType=LoadBalancer openfaas chart/openfaas
```
if set the rbac=false, there will be an error message.<br>
check the link https://github.com/openfaas/faas/issues/814
#### Destroy
```
helm delete --purge openfaas
```
<br>
related link:<br>
Deploying OpenFaaS on Kubernetes — Azure AKS: https://medium.com/@ericstoekl/deploying-openfaas-on-kubernetes-azure-aks-4eea99d0743f<br>
Helm and deploy from offical: https://github.com/openfaas/faas-netes/blob/master/HELM.md
