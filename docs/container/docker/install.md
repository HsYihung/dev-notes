# Docker 安裝

## macOS

### Docker Desktop（最常用）
- 安裝後請先開啟 Docker Desktop，確保引擎已啟動
```bash
docker --version
docker compose version
docker info
```

## Windows

### Docker Desktop（建議搭配 WSL2）
```powershell
docker --version
docker compose version
docker info
```

## Linux

### Debian / Ubuntu（以官方套件庫為主）
不同發行版的安裝方式略有差異，建議以官方文件為準；安裝後先確認：
```bash
docker --version
docker compose version
docker info
```

啟動並設為開機自動啟動（systemd）：
```bash
sudo systemctl enable --now docker
```

（可選）讓目前使用者不需要 `sudo`：
```bash
sudo usermod -aG docker "$USER"
```
完成後重新登入/重開機再測試：
```bash
docker run --rm hello-world
```

