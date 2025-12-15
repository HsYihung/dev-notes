# Kubernetes 工具生態圈

Kubernetes（K8s）是一套容器編排平台，負責把「容器化的應用」部署到叢集，並提供服務探索、擴縮、滾動更新、資源隔離等能力。

這裡整理常見的 K8s 周邊工具與它們各自負責的事情，並先把目錄建立起來，後續再補齊安裝與常用指令。

## 工具介紹（做什麼用）

### `kubectl`
- K8s 的官方 CLI，用來對 API Server 下指令
- 常用於：查資源（`get/describe`）、部署（`apply`）、除錯（`logs/exec/port-forward`）、切換 context/namespace

### `kubeadm`
- 官方的「叢集引導（bootstrap）」工具，用來在 Linux 節點上初始化 control plane、加入 worker
- 常用於：建立/擴充一個標準的 Kubernetes 叢集（偏「按步驟」與「可控」）

### `Kubespray`
- 以 Ansible 為核心的 Kubernetes 叢集部署工具（面向 Linux 多節點）
- 常用於：用 inventory 描述多台 Linux 主機，批次安裝/佈署/升級 Kubernetes（偏「自動化」與「一致性」）

### `minikube`
- 本機 Kubernetes 叢集工具（通常用於學習/開發驗證）
- 內部多半也是透過 `kubeadm` 等方式把叢集引導起來；如果你想把重點放在「理解 kubeadm 與叢集組成」，minikube 是很好用的練習環境

### `Helm`
- K8s 的套件管理器（Chart）
- 常用於：把一整套 K8s manifests 打包成可重用/可參數化的部署方案（values）、管理版本與升級回滾

### `Kustomize`
- Manifest 的「組裝/覆蓋」工具（base/overlay）
- 常用於：同一套 manifests 在 dev/staging/prod 用不同設定（patch、namePrefix、images、replicas）

### `containerd`
- 容器 runtime（K8s 節點上用來「拉 image、建立/啟動容器」的核心元件之一）
- K8s 透過 CRI（Container Runtime Interface）跟 runtime 溝通；containerd 是常見選擇
- 常見情境：節點層除錯（image/容器狀態）、理解 K8s 與容器 runtime 的邊界

## 目錄
- `docs/container/k8s/kubectl/README.md`
- `docs/container/k8s/kubeadm/README.md`
- `docs/container/k8s/kubespray/README.md`
- `docs/container/k8s/minikube/README.md`
- `docs/container/k8s/helm/README.md`
- `docs/container/k8s/kustomize/README.md`
- `docs/container/k8s/containerd/README.md`
