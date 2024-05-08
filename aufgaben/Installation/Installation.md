#openshift-kurs #installation

[Doku](https://docs.openshift.com/container-platform/4.15/installing/index.html)


#### Commands:

``` bash
cat pull-secret

ssh-keygen -t ed25519
cat .ssh/id_ed25519.pub

openshift-install version
openshift-install --help
openshift-install create  (ENTER)
openshift-install create install-config

cp install-config.yaml install-config.yaml_bak

openshift-install create cluster --log-level debug


```

#### Install Config
```bash
additionalTrustBundlePolicy: Proxyonly
apiVersion: v1
baseDomain: heinlein-akademie-openshift.de
compute:
- architecture: amd64
  hyperthreading: Enabled
  name: worker
  platform: {}
  replicas: 2
controlPlane:
  architecture: amd64
  hyperthreading: Enabled
  name: master
  platform: {}
  replicas: 3
metadata:
  creationTimestamp: null
  name: training0
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 10.0.0.0/16
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  aws:
    region: eu-central-1
publish: External
pullSecret: '{"auths":{"fake":{"auth": ""}}}'
```



