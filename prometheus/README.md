# Prometheus
## Install
### 1. PersistentVolume 생성
#### 1.1. Worker Node의 볼륨 생성
```
sudo mkdir -p /prometheus
```
```
sudo 777 -R /prometheus
```
```
vi /prometeus/prometheus-token
```
* Vault를 사용하기위한 권한이 있는 Token 값 입력(Root Token 가능)
* `persistentvolume.yaml`파일에서 `hostname`으로 지정한 Worker Node에서만 실행

#### 1.2. PersistentVolume 생성 및 실행
```
kubectl apply -f persistentvolume.yaml
```
* On-Prem 환경의 k8s에서 사용이 가능하도록 Local Storage PV 생
* Vault 생성 시 사용했던 StorageClass 함께 사

### 2. Helm을 통한 Prometheus Install
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```
```
helm install prometheus prometheus-community/prometheus -f values.yaml
```
* `values.yaml` 파일 경로
** `git clone https://github.com/prometheus-community/helm-charts`
** `cd helm-charts/charts/prometheus`
* `serverFiles.prometheus.yml.scrape_configs`에 `job_name: vault` 추가
** `static_configs`의 `targets` 항목에 `vault` 서비스의 IP:PORT 지정
```
helm list
```
