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
```
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-1
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /tmp
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - worker1
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-2
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /tmp
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - worker2
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-3
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /tmp
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - worker3
```
* On-Prem 환경의 k8s에서 사용이 가능하도록 Local Storage PV 생성
* Node 수량만큼 pV 생성
* `spec.local.path`: 데이터가 저장될 디렉토리
* `spec.nodeAffinity.required.nodeSelectorTerms.matchExpressions.values`: Storage를 할당할 Node의 hostname
