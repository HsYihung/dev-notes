# Kubespray

Kubespray 是以 Ansible 為核心的 Kubernetes 叢集部署工具，常用於在多台 Linux 主機上自動化安裝、部署與升級 Kubernetes。

適合情境：
- 你有一批 Linux 主機（實體機/VM/裸機），想用 inventory 一次性部署成 K8s 叢集
- 你想要可重複、可版本控管的叢集安裝流程（Git + inventory + playbooks）

TODO：
- 前置需求：SSH、Ansible、inventory 規劃
- 部署流程（建立 inventory、跑 playbook）
- 升級/節點擴容/縮容

