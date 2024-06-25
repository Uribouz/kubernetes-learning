### KillerCoda Kubernetes ConfigMap workshop:
https://killercoda.com/jumpbox/course/kubernetes-in-action/kubernetes-configmaps


### Create a configmap named config with values

controlplane $ kubectl create configmap config2 --from-literal=foo=lala --from-literal=foo2=lolo
configmap/config2 created

controlplane $ kubectl get configmap config2 -o yaml
    apiVersion: v1
    data:
    foo: lala
    foo2: lolo
    kind: ConfigMap
    metadata:
    creationTimestamp: "2024-06-25T03:23:01Z"
    name: config2
    namespace: default
    resourceVersion: "21108"
    uid: 364e63e0-9a88-43a8-9dbb-9a8df9f1da95

controlplane $ kubectl describe configmap config2
    Name:         config2
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>

    Data
    ====
    foo:
    ----
    lala
    foo2:
    ----
    lolo

    BinaryData
    ====

    Events:  <none>
controlplane $ 


### Create a configmap from a file
controlplane $ echo -e "foo3=lili\nfoo4=lele" > config.txt
controlplane $ cat config.txt
    foo3=lili
    foo4=lele
controlplane $ kubectl create configmap configmap3 --from-file=config.txt
    configmap/configmap3 created
controlplane $ kubectl describe configmap configmap3
    Name:         configmap3
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>

    Data
    ====
    config.txt:
    ----
    foo3=lili
    foo4=lele


    BinaryData
    ====

    Events:  <none>
controlplane $ 


### Create a configmap from a .env file
controlplane $ echo -e "var1=val1\n# this is a comment\n\nvar2=val2\n#anothercomment" > config.env
controlplane $ cat config.env
    var1=val1
    # this is a comment

    var2=val2
    #anothercomment
controlplane $ kubectl create configmap configmap4 --from-env-file=config.env
    configmap/configmap4 created
controlplane $ kubectl describe configmap configmap4
    Name:         configmap4
    Namespace:    default
    Labels:       <none>
    Annotations:  <none>

    Data
    ====
    var1:
    ----
    val1
    var2:
    ----
    val2

    BinaryData
    ====

    Events:  <none>
controlplane $ 

### Create a new nginx pod that loads values from config maps
controlplane $ kubectl create configmap options --from-literal=var5=val5
configmap/options created
controlplane $ k describe cm options
Name:         options
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
var5:
----
val5

BinaryData
====

Events:  <none>
controlplane $ kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx.yaml
controlplane $ vi nginx.yaml
```yaml
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
    resources: {}
  - env:
    - name: option
      valueFrom:
        configMapKeyRef:
          name: options
          key: var5
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
controlplane $ kubectl apply -f nginx.yaml
pod/nginx created
controlplane $ kubectl exec -it nginx -- env | grep option
option=val5
controlplane $ 
### Create a new nginx pod that loads config maps as environment variables
 $ kubectl create configmap anotherone --from-literal=var6=val6 --from-literal=var7=val7

 $ kubectl run nginx2 --image=nginx -o yaml --dry-run=client > nginx2.yaml
 $ vi nginx2.yaml

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    creationTimestamp: null
    labels:
        run: nginx2
    name: nginx2
    spec:
    containers:
    - image: nginx
        name: nginx2
        resources: {}
        envFrom:
        - configMapRef:
            name: anotherone
    dnsPolicy: ClusterFirst
    restartPolicy: Always
    status: {}
    ```

$ kubectl apply -f nginx2.yaml

$ kubectl exec -it nginx2 -- env | grep -E 'var7|var6'

### Create a new nginx pod that loads config maps as volumes
kubectl create configmap cmvolume --from-literal=var8=val8 --from-literal=var9=val9
configmap/cmvolume created
controlplane $ k describe cm cmvolume
Name:         cmvolume
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
var8:
----
val8
var9:
----
val9

BinaryData
====

Events:  <none>
controlplane $ kubectl run nginx3 --image=nginx -o yaml --dry-run=client > nginx3.yaml
controlplane $ vi nginx3.yaml


```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx3
  name: nginx3
spec:
  volumes: 
  - name: myvolume
    configMap:
      name: cmvolume
  containers:
  - image: nginx
    name: nginx3
    resources: {}
    volumeMounts:
    - name: myvolume
      mountPath: /etc/lala
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
controlplane $ kubectl run nginx3 --image=nginx -o yaml --dry-run=client > nginx3.yaml
controlplane $ vi nginx3.yaml
controlplane $ kubectl apply -f nginx3.yaml
pod/nginx3 created
controlplane $ kubectl exec -it nginx3 -- /bin/sh
# cd /etc/lala
# ls 
var8  var9
# 