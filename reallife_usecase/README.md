### DEMO Creating a system of 'nginx'
#### Setup deployment nginx 
$ kubectl create deployment nginx --image=nginx --dry-run=client -oyaml > nginx.yaml
$ kubectl apply -f nginx.yaml
$ k get svc nginx
NAME    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
nginx   ClusterIP   10.43.170.45   <none>        3000/TCP   24h
$ k get deploy nginx
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           87s
$ k expose deploy nginx
$ k expose deploy nginx --port=8080 --target-port=80 --dry-run=client -oyaml > nginx-svc.yaml
$ cat nginx-svc.yaml
<Output of file: 'nginx-svc.yaml'>
$ kubectl apply -f nginx-svc.yaml
#### Testing if nginx is working...
$ k get svc nginx
$ k get ep nginx
$ k run temp-pod --rm -it --image=nginx --restart=Never -- bash 
    $ curl nginx:8080
    <p><em>Thank you for using nginx.</em></p>
#### Creating ingress
$ kubectl create ingress nginx --rules=host/path=nginx:8080 -oyaml --dry-run=client > nginx-ing.yaml
$ cat nginx-ing.yaml
<Output of file: 'nginx-ing'>

$ kubectl get pod -A | grep -ie nginx
!!! missing nginx-controller ??
(IngressNginxController)[https://docs.nginx.com/nginx-ingress-controller/installation/ingress-nginx/]

$ kubectl apply -f nginx-ing.yaml
$ kubectl get ing nginx
NAME    CLASS   HOSTS   ADDRESS   PORTS   AGE
nginx   nginx   host              80      4m19s

WIP: should show "ADDRESS"