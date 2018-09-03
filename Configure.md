```

    rbac: {}
  channel: stable
  cloudProvider: aws
  configBase: s3://test.k8s.local-state.ym/test.k8s.local
  etcdClusters:
  - etcdMembers:
    - instanceGroup: master-us-west-1b
      name: b
    name: main
  - etcdMembers:
    - instanceGroup: master-us-west-1b
      name: b
    name: events
  iam:
    allowContainerRegistry: true
    legacy: false
  kubernetesApiAccess:
  - 0.0.0.0/0
  kubernetesVersion: 1.10.3
  masterInternalName: api.internal.test.k8s.local
  masterPublicName: api.test.k8s.local
  networkCIDR: 172.20.0.0/16
  networking:
    kubenet: {}
  nonMasqueradeCIDR: 100.64.0.0/10
  sshAccess:
  - 0.0.0.0/0
  subnets:
  - cidr: 172.20.32.0/19
    name: us-west-1b
    type: Public
    zone: us-west-1b
  topology:
    dns:
      type: Public
    masters: public
    nodes: public
```
