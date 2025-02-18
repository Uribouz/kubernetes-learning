# kubernetes-learning

## Workshop Link: https://eji4h.notion.site/Day-1-816b57ca9c3d4d43a145c42154b4b9cf
### Interactive Tutorial - Deploying an App

1. Get k8s client and server version
    ```bash
    kubectl version
    ```
    
2. List nodes in cluster
    
    ```bash
    kubectl get nodes
    ```
    
3. Create deployment name kubernetes-bootcamp
    
    ```bash
    kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
    ```
    
4. List all pod
    
    ```bash
    kubectl get pods 
    ```
    
5. Get pod name
    ```bash
    kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
    ```

### Interactive Tutorial - Exploring Your App

1. List pod
    
    ```bash
    kubectl get pods
    ```
    
2. Keep pod name to POD_NAME environment in first tab
    
    ```bash
    export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
    ```
    
3. echo name of pod in first tab
    
    ```bash
    echo Name of the Pod: $POD_NAME
    ```
    
4. Describe pods
    
    ```bash
    kubectl describe pods
    kubectl describe po/<POD_NAME> 
    ```
    
5. Click the new tab and go to a new terminal session
6. Keep pod name to POD_NAME environment in second tab
    
    ```bash
    export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
    ```
    
7. echo name of pod in second tab
    
    ```bash
    echo Name of the Pod: $POD_NAME
    ```
    
8. Proxy to k8s network
    
    ```bash
    kubectl port-forward $POD_NAME 8082:8080
    ```
    
9. Click the First tab to comeback the old terminal session
10. http request to pod
    
    ```bash
    curl localhost:8082
    ```
    
11. Get logs from pod
    
    ```bash
    kubectl logs -f $POD_NAME
    ```
    
12. Execute command directly to the container once the Pod
    
    ```bash
    kubectl exec $POD_NAME env
    ```

12-2. Execute any command
    
    ```bash
    kubectl exec $POD_NAME -- <ANY_COMMAND>
    ```

13. Bash session to the container once the Pod
    
    ```bash
    kubectl exec -it $POD_NAME bash
    ```
    
14. List all files in the current directory in the container
    
    ```bash
    ls -la
    ```
    
15. See NodeJS server.js file
    
    ```bash
    cat server.js
    ```
    
16. Http request inside container
    
    ```bash
    curl localhost:8080
    ```
    
17. Exit from container
    1. Type exit
        
        ```bash
        exit
        ```
        
    2. Ctrl + d

### Kubernetes Command Context

1. Config view
    
    ```bash
    kubectl config view
    ```
    
2. Get clusters
    
    ```bash
    kubectl config get-clusters
    ```
    
3. Get users
    
    ```bash
    kubectl config get-users
    ```
    
4. Get contexts
    
    ```bash
    kubectl config get-contexts
    ```
    
5. Current context
    
    ```bash
    kubectl config current-context
    ```
    
6. Set context
    
    ```bash
    kubectl config use-context rancher-desktop
    ```

### Using Imperative command to create Declarative command
#### Generating nginx.yaml of image 'nginx'
    ```bash
    kubectl run nginx --image nginx --dry-run=server -oyaml > nginx.yaml
    kubectl apply -f nginx.yaml
    ```
---


### Probe
#### StartupProbe + LivenessProbe
Following yaml 
1) will wait for 30*5 = 300 sec = 5min wait for app to startup 'startupProbe'
2) After 5 min of wait it will probeCheck the application liveness 'livenessProbe'

```yaml
startupProbe:
 httpGet:
 path: /healthz
 port: liveness-port
 failureThreshold: 30
 periodSeconds: 10
livenessProbe:
httpGet:
 path: /healthz
 port: liveness-port
failureThreshold: 1
periodSeconds: 10
```

### Resource Quota: Limit usage of resource in each POD
Link: https://blog.kubecost.com/blog/requests-and-limits/
- request หมายถึงว่า ขอให้ POD นี้มีค่า memory และ CPU อย่างน้อยเท่านี้
- limits หมายถึงว่า ถ้า POD นี้ใช้ memeory หรือ CPU เกิน ค่าที่กำหนด POD นั้นจะถูก restart ทันที

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-app
spec:
  containers:
  - name: wp
    image: wordpress
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

### Taints and Tolerations
Taints = แม่กุญแจ = การออกกฏหมายว่าตอนนี้ห้าม POD ประเภทไหนเข้ามา
Tolerations = ลูกกุญแจ = อลุ่มอะหล่วยว่า POD นี้สามารถเข้ามาทำงานได้

[Link](https://medium.com/kubernetes-tutorials/making-sense-of-taints-and-tolerations-in-kubernetes-446e75010f4e)

### Learning
- ReplicaSet ของ Kubernetes หมายถึง set ของ PODs ที่เมื่อ POD ตัวใดตัวหนึ่งตาย ระบบจะพยายาม restart pod ขึ้นมาให้ start ได้ทั้งหมด
- Service ของ Kubernetest มีการทำงานในการเลือก task ให้ POD โดยการดูว่า POD ไหนใช้ resource ที่น้อยที่สุดจะให้ POD ตัวนั้นได้รับไป, แต่ถ้าเท่ากันจะ random (ซึ่งสามารถปรับฯแก้ tune logic นี้ได้)
- POD = the smallest deployable units of Kubernetes
- ConfigMaps ของ Kubernetes จะช่วยให้เราจัดการ Configuration ของ POD ทุกตัว ได้ดีกว่า การใช้งาน Environment variable ตรงๆ (จะเป็น POD อีกตัวที่มี type เป็น ConfigMaps)
    + Allow you to decouple configuration from image content to keep containerized applications portable
    + It is used to setting configuration data separately from application code
    + It is like .env file which can vary on environments (dev, uat, and production)
    + Does not support large chunks of data.
    + The size of configmap cannot exceed 1MB.
- Secret ของ Kubernetes การใช้งาน เหมือนกับ ConfigMaps แต่จะช่วย encode base64 ของ environment value ที่เราเก็บไว้ (ซึ่งไม่ได้ปลอดภัยขนาดนั้น ถ้าข้อมูล Sensitive จริงๆ ควรใช้ Thirdparty library แทน)


#### Tips & Notes
- Container run time interface = ช่องทางในการรัน container
- Ingress ของ Kubernetes ในอนาคตอาจจะถูกแทนที่ด้วย API Gateway เช่น Nginx Gateway, etc...
- K8s คือชื่อเล่นของ Kubernetes
- Build one, Run anywhere คือหัวใจของ Docker
##### Graceful shutdown
- When you deploy a new version of your application, you must replace the previous version. The process manager you’re using will first send a SIGTERM signal to the application to notify it that it will be killed. Once the application gets this signal, it should stop accepting new requests, finish all the ongoing requests, clean up the resources it used, including database connections and file locks then exit.

```javascript
Example
const server = app.listen(port)

process.on('SIGTERM', () => {
  debug('SIGTERM signal received: closing HTTP server')
  server.close(() => {
    debug('HTTP server closed')
  })
})
```

##### Equivalent: Docker vs Kubernetes
    ```bash
    docker run --name nginx -d nginx
    ```
    --- Equal to ---
    ```bash
    kubectl run nginx --image nginx
    ```
##### Delete nginx pod
    ```bash
        kubectl delete pod nginx
    ```
##### Alias command 'k' = kubectl
    ```bash
        alias k=kubectl
    ```
##### You can list the available objects in your cluster with the following command:
    ```bash
        kubectl api-resources
    ```
##### If you want to know more about an object, kubectl has a built-in explanation function!. It can be used to learn more about how to write yaml file
    ```bash
        kubectl explain pod
        kubectl explain pod.spec
    ```
##### Tools to interact with Kubernetes via UI
- kubernetes/dashboard
- derailed/k9s
- Lens
- Rancher
- VMware Tanzu Octant
- Helm
##### Helm 
- Helm is a package manager for Kubernetes, which allows easier updates and interaction with objects. Helm packages Kubernetes objects in so-called Charts, which can be shared with others via a registry. To get started with Kubernetes, you can search the ArtifactHub to find your favorite software packages, ready to deploy
- Very useful when you want to use 'already-kustomize-template' from the already existing projects like 'nginx', Setup cluster of Nginx using 'Helm' [Link] (https://helm.nginx.com/)
- Useful site to explore existing 'Helm' packages [Link] (https://artifacthub.io/)
##### Beware Kubernetes user permission
- เก็บ Context ของ user ของคุณไว้ให้ดี
- อย่าให้สิทธิ user มากเกินความจำเป็น เช่นเป็น 'admin'

##### Link:
- Sidecar deployment strategy: https://istio.io/latest/docs/ops/deployment/architecture/
- eBPF deployment strategy: https://www.armosec.io/glossary/ebpf-kubernetes/
- What is Kubernetes: https://www.blognone.com/node/106492
- Helm: https://helm.sh/
- Kubernetes Replication Controller, Replica Set and Deployments: https://www.mirantis.com/blog/kubernetes-replication-controller-replica-set-and-deployments-understanding-replication-options#whatis
- Stress Learning: https://www.nature.com/articles/npjscilearn201611
- Kubectl Reference: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run
- Disposability: Fast startup and Graceful shutdown https://12factor.net/disposability
- Kubernetes private registry: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account
- Kustomize lets you customize raw, template-free YAML files for multiple purposes, leaving the original YAML untouched and usable as is.: https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/
- Kubernetes Role management: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
- UI for Kubernetes role mangagement: https://k9scli.io/
- Kustomize help: https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/
- SOPS: Secrets OPerationS: https://github.com/getsops/sops
- 'daily-dev' site to read news about developer: https://daily.dev/
- การจัดการ Resource Quotas: https://frankdenneman.nl/2020/03/20/scheduling-vsphere-pods/
- Let's Encrypt จัดการ certificate ของ DomainName website ของคุณ: https://letsencrypt.org/
- Kubernetes CheatSheet: https://web.facebook.com/acloudguru/posts/the-ultimate-kubernetes-cheat-sheet-save-and-share-plus-register-for-the-free-ku/3547894078603673/?_rdc=1&_rdr
