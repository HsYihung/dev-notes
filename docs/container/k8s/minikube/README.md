# minikube（以 kubeadm 觀點為主）

minikube 是用來在本機快速啟動 Kubernetes 叢集的工具；它適合做開發/學習/驗證。

如果你的目標是「部署 Linux 叢集」：正式環境通常會以 `kubeadm`（手動/半自動）或 `Kubespray`（自動化）為主；而 minikube 可以當作練習場，幫你先把 kubeadm 的概念跑過一次。

## 安裝（概念）
不同作業系統與 driver（Docker/VM）會影響安裝方式；以官方文件為準。

安裝後確認：
```bash
minikube version
kubectl version --client
```

## 啟動叢集（把它當成 kubeadm 練習場）

建立一個本機 cluster：
```bash
minikube start
```

指定 Kubernetes 版本（練習升級/差異時常用）：
```bash
minikube start --kubernetes-version=v1.30.0
```

指定 driver（常見：docker）：
```bash
minikube start --driver=docker
```

## kubeadm 相關觀察點（你在正式叢集也會遇到）

### 1) Control plane / worker 的概念與元件
- apiserver / controller-manager / scheduler / etcd
- kubelet：每個 node 都有，負責跟 runtime（例如 containerd）協作跑 Pod
- CNI：網路插件（Pod-to-Pod 通訊）

### 2) 與 `kubeadm` 的關係
minikube 的重點是「幫你把叢集啟起來」，而 `kubeadm` 的重點是「你可控地一步步初始化叢集」。

你可以把下面這些問題當作練習題（對應到 kubeadm 真實工作）：
- 叢集起來後，哪些 Namespace/Pod 是系統元件？
- CNI 是哪一個？DNS 是哪一個？它們是否 Ready？
- 如果控制平面掛了，你會用 `kubectl` 看事件/看 log 從哪裡查起？

## 常用指令

查看狀態：
```bash
minikube status
kubectl get nodes -o wide
kubectl get pods -A
```

進入 dashboard（可選）：
```bash
minikube dashboard
```

停止/刪除：
```bash
minikube stop
minikube delete
```

## TODO
- 實作：用 minikube 練習一遍 kubeadm 初始化後的常用除錯流程（node/pod/network/dns）
- 補充：正式環境的 kubeadm 部署步驟請看 `docs/container/k8s/kubeadm/README.md`

