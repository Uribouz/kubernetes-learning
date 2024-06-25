### KillerCoda Kubernetes ConfigMap workshop:
https://killercoda.com/jumpbox/course/kubernetes-in-action/kubernetes-health-check-probe


$ cd liveness
$ k apply -f liveness.yaml
$ kubectl describe pod liveness-exec
$ k get pods -w liveness-exec
NAME            READY   STATUS    RESTARTS   AGE
liveness-exec   1/1     Running   0          97s
liveness-exec   1/1     Running   1 (19s ago)   119s
liveness-exec   1/1     Running   2 (20s ago)   3m30s


$ k apply -f readiness.yaml
$ kubectl describe pod readiness-exec
$ kubectl get pods -w readiness-exec
NAME             READY   STATUS    RESTARTS   AGE
NAME             READY   STATUS    RESTARTS   AGE
readiness-exec   0/1     Running   0          6m16s
readiness-exec   0/1     Completed   0          10m
readiness-exec   0/1     Running     1 (18s ago)   11m
readiness-exec   1/1     Running     1 (27s ago)   11m
readiness-exec   0/1     Running     1 (62s ago)   11m