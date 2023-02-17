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
kubectl apply -f ./yaml/storageclass.yaml
```

### 3. PersistentVolume 생성
#### 3.1. Worker Node의 볼륨 생성
```
mkdir -p /vault/data
```
```
chmod 777 -R /vault
```
* Pod가 생성될 Worker Node 3대에서 각각 실행

#### 3.2. PersistentVolume 생성 및 실행
```
kubectl apply -f ./yaml/persistentvolume.yaml
```
* On-Prem 환경의 k8s에서 사용이 가능하도록 Local Storage PV 생성
* Node 수량만큼 PV 생성

### 4. Helm을 통한 Vault Install
```
helm repo add hashicorp https://helm.releases.hashicorp.com
```
```
helm install vault hashicorp/vault -f values.yaml
```
```
helm list
```

## Init & Unseal
### 1. Init
vault-0 Pod 접속
```
kubectl exec -it vault-0 sh
```
```
vault operator init -key-shares=1 -key-threshold=1 | tee /tmp/vault-initialization.txt
```
```
vault operator unseal <Unseal Key 입력>
```
```
vault login <Token 입력>
```

vault-1 Pod 접속
```
kubectl exec -it vault-1 sh
```
```
vault operator raft join http://vault-0.vault-internal:8200
```
```
vault operator unseal <Unseal Key 입력>
```
```
vault login <Token 입력>
```

vault-2 Pod 접속
```
kubectl exec -it vault-2 sh
```
```
vault operator raft join http://vault-0.vault-internal:8200
```
```
vault operator unseal <Unseal Key 입력>
```
```
vault login <Token 입력>
```
HA 구성 조회
```
vault operator raft list-peers
```
