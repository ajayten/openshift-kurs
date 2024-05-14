#openshift-kurs 

[Openshift-Documenation](https://docs.openshift.com/container-platform/4.15/welcome/index.html)

#### bash completion

```bash
oc completion bash > oc_bash_completion
cp oc_bash_completion /etc/bash_completion.d/
source .bashrc
```

#### CLI

``` bash
oc login
oc logout

oc get ... --kubeconfig=auth/kubeconfig

export KUBECONFIG

oc whoami --show-server
```


#### Web Console

Access the OpenShift web-console here: https://console-openshift-console.apps.training0.heinlein-akademie-openshift.de

#### htpasswd

https://docs.openshift.com/container-platform/4.15/authentication/identity_providers/configuring-htpasswd-identity-provider.html

**Aufgabe:**
- Konfiguriere einen htpasswd Identity Provider
- Füge einen 2. User hinzu
- 

``` bash
create htpasswd file:
htpasswd -c -B -b htpasswd_file Testuser test123

create secret:
oc create secret generic htpass-secret --from-file=htpasswd=htpasswd_file -n openshift-config
```

**htpasswd Custom Ressource:**
``` bash

htpasswd_provider.yaml

apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: htpasswd_provider 
    mappingMethod: claim 
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret


oc apply -f htpasswd_provider.yaml

oc get pods -n openshift-authentication

```

**Updating Users htpasswd:
``` bash
oc get secret htpass-secret -ojsonpath={.data.htpasswd} -n openshift-config | base64 -d > users.htpasswd

htpasswd -bB users.htpasswd Testuser2 test123123

create secret yaml:

oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd -n openshift-config --dry-run=client -o yaml > htpasswd-secret.yaml

oc replace -f htpasswd-secret.yaml

oc get pods -n openshift-authentication
```

**delete User:**
```bash
oc get secret htpass-secret -ojsonpath={.data.htpasswd} -n openshift-config | base64 -d > users.htpasswd
htpasswd -D users.htpasswd Testuser
htpasswd -D users.htpasswd Testuser2

oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd --dry-run=client -o yaml -n openshift-config | oc relace -f -

oc get pods -n openshift-authentication
```

**delete User Object! :**
``` bash
oc get user
oc get identities.user.openshift.io

oc delete user Testuser Testuser2

oc delete identity htpasswd_provider:Testuser htpasswd_provider:Testuser2
```

#### Simple Nginx Pod running
```bash
oc run nginx --image=nginx --port=80 --dry-run=client -oyaml > nginx.yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

oc rsh nginx

curl localhost


```

#### Expose Pod
``` bash
oc expose pod/nginx

oc get svc
# nginx        ClusterIP      172.30.68.255   <none>                                 80/TCP    7s

## auf dem Test Pod
oc exec -it test curl 10.128.2.19 # die Pod IP

oc exec -it test curl nginx.default.svc.cluster.local # service

oc expose svc nginx

oc get route nginx -ojson|jq -r '.spec.host' # get hostname

# vom Host / Browser
curl nginx-default.apps.training0.heinlein-akademie-openshift.de
```

#### Storage

- erstelle einen neuen PVC (1 Gi) - wie ist der Status und warum=
- nutze diesen Claim in einem Pod
- erstelle eine neue Storage Class - Binding Mode Immediate
- teste mit einem neuen PVC




#### Networkpolicies


