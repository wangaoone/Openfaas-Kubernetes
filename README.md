# K8s

### set up AWS and IAM
#### setup the aws cli, kubectl and kops<br>
```Bash
$ curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl
$ sudo mv kubectl /usr/local/bin/
$ wget https://github.com/kubernetes/kops/releases/download/1.8.0/kops-linux-amd64
$ chmod +x kops-linux-amd64
$ sudo mv kops-linux-amd64 /usr/local/bin/kops
$ sudo apt-get install awscli
$ sudo pip install awscli
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
$ Default region name [None]: us-east-1
$ Default output format [None]: json
```
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
$ export NAME=ao.k8s.local
```
I do not know why the cluster.k8s.local has existed.<br>
create s3 bucket

```bash
$ aws s3api create-bucket --bucket ${NAME}-state.ym --create-bucket-configuration LocationConstraint=$AWS_REGION
$ export KOPS_STATE_STORE=s3://ao.k8s.local-state.ym
```

#### deploy K8s Cluster
创建集群的配置文件，不会真正地创建集群：
```bash
$ kops create cluster  --name=${NAME}  zones=us-east-1b  master-size="t2.micro" \
--node-size="t2.micro"  --ssh-public-key="~/.ssh/id_rsa.pub"
```
*The zones name should be the full name from aws.<br>*
```bash
$ aws ec2 describe-availability-zones --region us-east-1
```
