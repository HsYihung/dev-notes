# containerd

`containerd` 是一個工業級的容器 runtime，負責「拉取/管理 image、建立/啟動容器、管理容器生命週期」等工作。

Kubernetes 在每個節點上會透過 CRI（Container Runtime Interface）呼叫容器 runtime；`containerd` 是最常見的選擇之一（K8s 也常搭配 `runc` 作為 OCI runtime）。

## 架構與名詞（快速理解）
- `kubelet`：節點上的代理，負責跑 Pod；會透過 CRI 呼叫 runtime
- `containerd`：CRI 的其中一種實作，負責 image/容器管理
- `runc`：OCI runtime，真正負責建立 Linux namespace/cgroup 並啟動容器 process
- `shim`：containerd 會透過 shim 管理容器程序，讓 containerd 本身可重啟而不影響容器

## 常用工具（Debug 時會用到）
- `ctr`：containerd 原生 CLI（偏底層，**不一定**完全符合 K8s/CRI 的視角）
- `crictl`：以 CRI 視角操作 runtime（更貼近 kubelet 看到的世界）
- `nerdctl`（可選）：比較接近 `docker` 使用體驗的 CLI（底層仍是 containerd）

## 安裝（Linux）

以下以「先把 containerd 跑起來」為主；若你要讓 Kubernetes 使用，通常還需要搭配節點前置（cgroup、sysctl、CNI 等）。

### Ubuntu / Debian
```bash
sudo apt update
sudo apt install -y containerd
containerd --version
```

啟動並設成開機自動啟動：
```bash
sudo systemctl enable --now containerd
systemctl status containerd --no-pager
```

（建議）產生預設設定檔：
```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null
```

（K8s 常用）設定 systemd cgroup driver：
- 編輯 `/etc/containerd/config.toml`，把：
  - `SystemdCgroup = false` 改成 `SystemdCgroup = true`
```bash
sudo rg -n "SystemdCgroup" /etc/containerd/config.toml || true
sudo systemctl restart containerd
```

（可選）安裝 `crictl`：
```bash
sudo apt install -y cri-tools
crictl --version
```

### Fedora / RHEL / CentOS（若發行版提供套件）
```bash
sudo dnf install -y containerd
sudo systemctl enable --now containerd
containerd --version
```

## 驗證與基本除錯

### 檢查 daemon
```bash
systemctl status containerd --no-pager
journalctl -u containerd -n 200 --no-pager
```

### 以 CRI 視角查看（若已安裝 `crictl`）
```bash
sudo crictl info
sudo crictl ps -a
sudo crictl images
```

## 常見問題

### kubelet 起不來 / cgroup driver 不一致
在 systemd 的主機上，K8s 常用 `SystemdCgroup = true`；若 runtime/kubelet cgroup driver 不一致，可能造成 kubelet 啟動失敗或資源管理異常。

