 #### 1) Imperative command to create Declarative command for creating "ConfigMap"
 ```bash
 $ kubectl create configmap config-nong-kai --from-literal=db_user=admin --from-literal=db_password=test --dry-run=client -oyaml > nongkai-cm.yaml
 ```

 #### 2) Check content of the yaml file.
 ```bash
 $ cat nongkai-cm.yaml
 ```
 ```yaml
    apiVersion: v1
    data:
    db_password: test
    db_user: admin
    kind: ConfigMap
    metadata:
    creationTimestamp: null
    name: config-nong-kai
```
 
 ### 3) Create the POD configMap from file "nongkai-cm.yaml"
 ```bash
 $ kubectl apply -f nongkai-cm.yaml
 configmap/config-nong-kai created
 ```
 
 ### 4) Check if the pod start
 ```bash
 $ kubectl get cm
    NAME               DATA   AGE
    config             2      2m24s
    config-nong-kai    2      10s
    kube-root-ca.crt   1      16d
 ```


 kubectl run nginx5 --image=nginx --dry-run=client -o yaml > nginx5.yaml

 vi nginx5.yaml

 Under container section add following line

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    creationTimestamp: null
    labels:
        run: nginx5
    name: nginx5
    spec:
    containers:
    - image: nginx
        name: nginx5
        resources: {}
        envFrom:
        - configMapRef:
            name: config
    dnsPolicy: ClusterFirst
    restartPolicy: Always
    status: {}
    ```

    ```bash
    $ kubectl apply -f nginx5.yaml
    ```

    ```bash
    $ kubectl get pods
    ```

    ```bash
    $ kubectl exec -it nginx5 env  | grep -E 'db_user|db_password'
    kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
    db_password=test
    db_user=admin
    ```






    