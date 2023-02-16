# k8s-vault-prometheus
## Prerequisite
* k8s Cluster

## 1. License Secret 생성
```
secret=$(cat ./license/tmp-license.hclic)
kubectl create secret generic ent-license --from-literal="license=${secret}"
```
