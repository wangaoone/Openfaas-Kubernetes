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
