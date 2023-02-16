# k8s-vault-prometheus
## Prerequisite
* k8s Cluster
  * 3 Node 이상 구성
* Helm

## Install
### 1. License Secret 생성
```
secret=$(cat ./license/tmp-license.hclic)
kubectl create secret generic ent-license --from-literal="license=${secret}"
```

### 2. StorageClass 생성
```
kubectl apply -f storageclass.yaml
```

### 3. PersistentVolume 생성
```
kubectl apply -f persistentvolume.yaml
```
* On-Prem 환경의 k8s에서 사용이 가능하도록 Local Storage PV 생성
