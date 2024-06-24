```bash
    kubectl run nginx --image nginx --dry-run=server -oyaml > nginx.yaml
    kubectl apply -f nginx.yaml
```