# Git 安裝

## macOS

### 方式 1：Xcode Command Line Tools（通常已內建 Git）
```bash
xcode-select --install
git --version
```

### 方式 2：Homebrew
```bash
brew install git
git --version
```

## Windows

### 方式 1：Git for Windows
- 安裝後可使用 `Git Bash`、`cmd`、`PowerShell` 直接執行 `git`
```powershell
git --version
```

### 方式 2：WSL（在 Linux 子系統內安裝）
```bash
sudo apt update
sudo apt install -y git
git --version
```

## Linux

### Ubuntu / Debian
```bash
sudo apt update
sudo apt install -y git
git --version
```

### Fedora
```bash
sudo dnf install -y git
git --version
```

### CentOS / RHEL
```bash
sudo yum install -y git
git --version
```

### Arch
```bash
sudo pacman -S git
git --version
```

## 初始設定（建議）
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"
git config --global pull.rebase false
```

查看設定：
```bash
git config --global --list
```

