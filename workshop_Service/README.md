### KillerCoda Kubernetes Deployment Service workshop:
- https://killercoda.com/jumpbox/course/kubernetes-in-action/kubernetes-services
    Create nginx Deployment
    ```bash
    kubectl create deploy nginx --image nginx --dry-run=client -o yaml > nginx.yaml
    kubectl apply -f nginx.yaml
    ```

    Create nginx service from Deployment
    ```bash
    kubectl expose deploy nginx --port 3000 --target-port 80 --dry-run=client -o yaml > nginx-svc.yaml
    kubectl apply -f nginx-svc.yaml
    ```

    Get status of pod
    ```bash
    kubectl get svc,deploy,rs,pod | grep nginx
    ```

    Get IP of a pod
    ```bash
    kubectl describe pod/nginx-7854ff8877-86h9c | grep IP
    ```

    Get endpoints of pod
    ```bash
    kubectl get ep,svc,deploy,rs,pod | grep nginx
    ```