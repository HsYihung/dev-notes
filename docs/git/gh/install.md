# gh 安裝（GitHub CLI）

安裝完成後先確認版本：
```bash
gh --version
```

## macOS

### Homebrew
```bash
brew install gh
gh --version
```

## Windows

### winget
```powershell
winget install --id GitHub.cli
gh --version
```

### Chocolatey
```powershell
choco install gh -y
gh --version
```

## Linux

### Ubuntu / Debian（apt）
```bash
sudo apt update
sudo apt install -y gh
gh --version
```

### Fedora / RHEL（dnf）
```bash
sudo dnf install -y gh
gh --version
```

### Arch
```bash
sudo pacman -S github-cli
gh --version
```

## 初始設定（登入）

互動式登入（最常用）：
```bash
gh auth login
```

查看登入狀態：
```bash
gh auth status
```

切換/新增帳號（多帳號時）：
```bash
gh auth switch
```

