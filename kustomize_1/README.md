### Link doc: https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/configmapgenerator
```bash
$ create file "kustomization.yaml"
```
```bash
$ echo "FOO=bar" > application.properties
```
```bash
$ cat application.properties
FOO=bar
```
```bash
$ kubectl apply -k .
configmap/payment created
```
```bash
$ kubectl get -k .
NAME      DATA   AGE
payment   1      2s
```

### Link Doc: 
https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/resource/

```bash
$ kubectl create deployment nginx --image=nginx --dry-run=client -oyaml > deploy.yaml
```

#### แก้ไขไฟล์ kustomization.yaml เพิ่มบรรทัดพวกนี้เข้าไป
```yaml
commonLabels:
  app: nong-kai
  env: prod

# Deployment
replicas:
- name: my-nginx
  count: 5

resources:
- deploy.yaml
```

```bash
$ kubectl apply -k .
deployment.apps/nginx configured
```

```bash
$ kubectl get -k .
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           18h
```

### Generate file manifest.yaml
```bash
$ kubectl kustomize > my-manifest.yaml
```