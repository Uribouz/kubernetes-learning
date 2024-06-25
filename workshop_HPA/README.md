### KillerCoda Kubernetes Rolling-Update workshop:

https://killercoda.com/jumpbox/course/kubernetes-in-action/kubernetes-hpa


#### Tutorial
$ kubectl create deployment eureka --image=netflixoss/eureka:1.3.1 --dry-run=client -o yaml > eureka.yaml

edit eureka.yaml add 
```yaml
        resources:
          requests:
            memory: "128Mi"
            cpu: "50m"
          limits:
            memory: "512Mi"
            cpu: "150m"
```

$ kubectl apply -f eureka.yaml

$ kubectl describe pods -l app=eureka
 

$ kubectl autoscale deployment eureka --cpu-percent=20 --min=1 --max=10 --dry-run=client -oyaml > hpa.yaml

$ kubectl get hpa -w
NAME     REFERENCE           TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
eureka   Deployment/eureka   <unknown>/20%   1         10        0          10s
eureka   Deployment/eureka   <unknown>/20%   1         10        1          15s

$ kubectl get pod -w
NAME                                  READY   STATUS              RESTARTS         AGE
eureka-5544dfbbf-f6f9c                0/1     ContainerCreating   0                2m56s
eureka-5544dfbbf-f6f9c                0/1     ErrImagePull   0                5m49s


### Assignment
#### 1) Run and expose php-apache server
create file 'php-apache.yaml'

$ kubectl apply -f php-apache.yaml

$ kubectl get pods
NAME                                  READY   STATUS              RESTARTS       AGE
php-apache-598b474864-5kwhg           0/1     ContainerCreating   0              5s
php-apache-598b474864-5kwhg           1/1     Running            0               2m25s

#### 2) Create the HorizontalPodAutoscaler

$ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10 --dry-run=client -oyaml > hpa.yaml

$ kubectl apply -f hpa.yaml
$ kubectl get hpa -w
NAME         REFERENCE               TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   <unknown>/50%   1         10        0          3s
php-apache   Deployment/php-apache   0%/50%          1         10        1          15s

#### 3) Increase the load and see the result
$ kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"

$ kubectl get hpa php-apache --watch
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   249%/50%   1         10        4          2m31s
php-apache   Deployment/php-apache   188%/50%   1         10        5          2m45s
php-apache   Deployment/php-apache   65%/50%    1         10        5          3m
php-apache   Deployment/php-apache   56%/50%    1         10        5          3m16s

$ kubectl get deployment php-apache
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
php-apache   1/1     1            1           5m41s
-->
php-apache   5/5     5            5           6m28s

#### 4) Stop generating load

Ctrl-C program load-generator

$ kubectl get hpa php-apache --watch
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   48%/50%   1         10        6          7m33s
php-apache   Deployment/php-apache   12%/50%   1         10        6          7m46s
php-apache   Deployment/php-apache   0%/50%    1         10        6          8m1s

$ kubectl get deployment php-apache
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
php-apache   6/6     6            6           12m
php-apache   1/1     1            1           22m

#### TIP:
##### fix cannot connect to metric-server (cannot see the % usage of 'TARGETS' of command '$ kubectl get hpa')
1) edit file 'metric-server.yaml', add following lines
```yaml
  - args:
    - --kubelet-insecure-tls
```
2) then apply 
$ kubectl apply -f metric-server.yaml