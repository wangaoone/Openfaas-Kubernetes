# K8s

### set up AWS and IAM
#### setup the aws cli, kubectl and kops<br>
linux
```Bash
$ curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl
$ sudo mv kubectl /usr/local/bin/
$ wget https://github.com/kubernetes/kops/releases/download/1.8.0/kops-linux-amd64
$ chmod +x kops-linux-amd64
$ sudo mv kops-linux-amd64 /usr/local/bin/kops
$ sudo apt-get install awscli
$ sudo pip install awscli
```
OSX: setup aws cli, kubetcl, kops
```
$ kops version
```

#### configure the AWS account
```Bash
$ aws configure
$ AWS Access Key ID [None]: <your-accesskeyID>
$ AWS Secret Access Key [None]: <your-secretAccessKey>
$ Default region name [None]: us-east-1
$ Default output format [None]: json
```
#### Create a new role called cops in the IAM and grant following permissions:
```bash
$ aws iam create-group --group-name kops
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name kops
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess --group-name kops
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --group-name kops
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMFullAccess --group-name kops
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess --group-name kops
$ aws iam create-user --user-name kops
$ aws iam add-user-to-group --user-name kops --group-name kops
```
#### create the key for kops and configure the aws with the new key
```bash
$ aws iam create-access-key --user-name kops
$ aws configure
$ AWS Access Key ID [None]: <accesskeyID-of-kops-user>
$ AWS Secret Access Key [None]: <secretAccessKey-of-kops-user>
$ Default region name [None]: us-west-1
$ Default output format [None]: json
```
could not use the region at us-east-1
#### set the terminal enviornment variables
```bash
$ export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
$ export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)
$ export AWS_REGION=$(aws configure get region)
```
#### get ssh
```bash
$ ssh-keygen
```
### set S3 bucket
  为了让 kops 创建基于 gossip 的集群，集群的命名需要使用.k8s.local作为后缀，例如，这里我们将集群命名为cluster.k8s.local
```bash
$ export NAME=test.k8s.local
```
I do not know why the cluster.k8s.local has existed.<br>
try not use the same name of the s3 bucket.
create s3 bucket

```bash
$ aws s3api create-bucket --bucket ${NAME}-state.ym --create-bucket-configuration LocationConstraint=$AWS_REGION
$ export KOPS_STATE_STORE=s3://test.k8s.local-state.ym
```
```
{
    "Location": "http://test.k8s.local-state.ym.s3.amazonaws.com/"
}
```

#### deploy K8s Cluster
创建集群的配置文件，不会真正地创建集群：
```bash
$ kops create cluster  --name=${NAME}  --zones=us-west-1b  --master-size="t2.micro" \
--node-size="t2.micro"  --ssh-public-key="~/.ssh/id_rsa.pub"
```
```
kops create cluster --name=${NAME} --zones=us-west-2a --master-size="t2.micro" --node-size="t2.micro" --node-count="3" --ssh-public-key="~/.ssh/id_rsa.pub"
 ```
 ```
 kops create cluster --name=${NAME} --zones=us-west-1b --master-size="t2.medium" --node-size="t2.medium" --node-count="4" --ssh-public-key="~/.ssh/id_rsa.pub"
 ```
*The zones name should be the full name from aws.<br>*
```bash
$ aws ec2 describe-availability-zones --region us-east-1
```

在创建集群之前，可以检查集群的配置文件是否正确:<br>
```bash
$ kops edit cluster ${NAME}
```
如果确认没问题，就可以使用下面的命令创建集群:<br>
```bash
$ kops update cluster ${NAME} --yes
```
Testing the cluster
```bash
$ kops validate cluster
```
Delete cluster
```
kops delete cluster --name ${NAME} --yes
```

#### Links
Installing Kubernetes on AWS<br>https://medium.com/containermind/how-to-create-a-kubernetes-cluster-on-aws-in-few-minutes-89dda10354f4<br>
使用kops在AWS部署Kubernetes 集群<br>http://senlinzhan.github.io/2018/01/11/k8s-on-aws/<br>
#### config multiple clusters
```
kubectl config use-context first.k8s.local
```
