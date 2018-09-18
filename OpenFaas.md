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
$ helm init --upgrade
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

Use this cmd to check the status of openfaas and use external-ip of gateway-external to get the openfaas ui<br>
```
$ kubectl get all -n openfaas
  NAME                                READY     STATUS    RESTARTS   AGE
  pod/alertmanager-5ddb544965-z7lrp   1/1       Running   0          1m
  pod/gateway-84456688b4-m27bl        2/2       Running   1          1m
  pod/nats-86955fb749-zw9jf           1/1       Running   0          1m
  pod/prometheus-6599674f5-tsjg6      1/1       Running   0          1m
  pod/queue-worker-54c65bc697-9jrgf   1/1       Running   1          1m

  NAME                       TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)          AGE
  service/alertmanager       ClusterIP      100.71.12.153    <none>                                                                   9093/TCP         1m
  service/gateway            ClusterIP      100.64.75.126    <none>                                                                   8080/TCP         1m
  service/gateway-external   LoadBalancer   100.67.71.96     a20c495c8afe811e880b0062ffed5884-126380147.us-west-            1.elb.amazonaws.com   8080:32673/TCP   1m
  service/nats               ClusterIP      100.68.131.177   <none>                                                                   4222/TCP         1m
  service/prometheus         ClusterIP      100.65.115.40    <none>                                                                   9090/TCP         1m

  NAME                           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
  deployment.apps/alertmanager   1         1         1            1           1m
  deployment.apps/gateway        1         1         1            1           1m
  deployment.apps/nats           1         1         1            1           1m
  deployment.apps/prometheus     1         1         1            1           1m
  deployment.apps/queue-worker   1         1         1            1           1m

  NAME                                      DESIRED   CURRENT   READY     AGE
  replicaset.apps/alertmanager-5ddb544965   1         1         1         1m
  replicaset.apps/gateway-84456688b4        1         1         1         1m
  replicaset.apps/nats-86955fb749           1         1         1         1m
  replicaset.apps/prometheus-6599674f5      1         1         1         1m
  replicaset.apps/queue-worker-54c65bc697   1         1         1         1m
```

#### Destroy
```
helm delete --purge openfaas
```
<br>
Related link:<br>
Deploying OpenFaaS on Kubernetes — Azure AKS:<br>https://medium.com/@ericstoekl/deploying-openfaas-on-kubernetes-azure-aks-4eea99d0743f<br>
Helm and deploy from offical:<br>https://github.com/openfaas/faas-netes/blob/master/HELM.md<br>
OpenFaaS - Serverless Functions Made Simple<br>https://github.com/openfaas/faas-netes/blob/master/chart/openfaas/README.md<br>





###### 几个命令
```
kubectl get pods -n kube-system -o wide
```
### logs
```
ip-172-20-37-165  master

ip-172-20-48-53   node

ip-172-20-48-53   node
```

```
AodeMacBook-Pro:faas-cli wang$ kubectl get pods --all-namespaces -o wide
NAMESPACE     NAME                                                                  READY     STATUS    RESTARTS   AGE       IP              NODE
kube-system   dns-controller-6d6b7f78b-45r2n                                        1/1       Running   0          51m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   etcd-server-events-ip-172-20-37-165.us-west-1.compute.internal        1/1       Running   0          50m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   etcd-server-ip-172-20-37-165.us-west-1.compute.internal               1/1       Running   0          51m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   kube-apiserver-ip-172-20-37-165.us-west-1.compute.internal            1/1       Running   0          50m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   kube-controller-manager-ip-172-20-37-165.us-west-1.compute.internal   1/1       Running   0          50m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   kube-dns-5fbcb4d67b-2nxl5                                             3/3       Running   0          49m       100.96.2.3      ip-172-20-48-53.us-west-1.compute.internal
kube-system   kube-dns-5fbcb4d67b-cldc6                                             3/3       Running   0          51m       100.96.1.2      ip-172-20-56-102.us-west-1.compute.internal
kube-system   kube-dns-autoscaler-6874c546dd-fp7wn                                  1/1       Running   0          51m       100.96.2.2      ip-172-20-48-53.us-west-1.compute.internal
kube-system   kube-proxy-ip-172-20-37-165.us-west-1.compute.internal                1/1       Running   0          50m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   kube-proxy-ip-172-20-48-53.us-west-1.compute.internal                 1/1       Running   0          50m       172.20.48.53    ip-172-20-48-53.us-west-1.compute.internal
kube-system   kube-proxy-ip-172-20-56-102.us-west-1.compute.internal                1/1       Running   0          49m       172.20.56.102   ip-172-20-56-102.us-west-1.compute.internal
kube-system   kube-scheduler-ip-172-20-37-165.us-west-1.compute.internal            1/1       Running   0          51m       172.20.37.165   ip-172-20-37-165.us-west-1.compute.internal
kube-system   kubernetes-dashboard-7b9c7bc8c9-tpqcj                                 1/1       Running   0          46m       100.96.2.4      ip-172-20-48-53.us-west-1.compute.internal
kube-system   tiller-deploy-895d57dd9-5pjgs                                         1/1       Running   0          38m       100.96.2.5      ip-172-20-48-53.us-west-1.compute.internal
openfaas      alertmanager-57d776f497-nfpkm                                         1/1       Running   0          38m       100.96.1.4      ip-172-20-56-102.us-west-1.compute.internal
openfaas      gateway-756d7bd4c6-99749                                              2/2       Running   2          38m       100.96.2.6      ip-172-20-48-53.us-west-1.compute.internal
openfaas      nats-86955fb749-dw6qc                                                 1/1       Running   0          38m       100.96.1.7      ip-172-20-56-102.us-west-1.compute.internal
openfaas      prometheus-766c8b79-jd27h                                             1/1       Running   0          38m       100.96.1.6      ip-172-20-56-102.us-west-1.compute.internal
openfaas      queue-worker-5b6487c99-zvx8h                                          1/1       Running   0          38m       100.96.1.5      ip-172-20-56-102.us-west-1.compute.internal
openfaas-fn   figlet-675bf95f4f-bkvml                                               1/1       Running   0          31m       100.96.2.7      ip-172-20-48-53.us-west-1.compute.internal
openfaas-fn   nodejs-echo-6c4b497967-mrsc4                                          1/1       Running   0          15m       100.96.2.8      ip-172-20-48-53.us-west-1.compute.internal
openfaas-fn   shrink-image-84f684c45-bmt4z                                          1/1       Running   0          18m       100.96.1.8      ip-172-20-56-102.us-west-1.compute.internal

$ kubectl describe -n=openfaas pod kube-scheduler-ip-172-20-37-165.us-west-1.compute.internal
```
