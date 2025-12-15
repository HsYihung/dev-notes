# Git 常用指令

## 檢查狀態與差異
```bash
git status
git diff
git diff --staged
```

## 初始化 / 複製專案
```bash
git init
git clone <repo_url>
```

## 基本提交流程
```bash
git add -A
git commit -m "message"
```

常用 `add`：
```bash
git add <file>
git add .
git restore --staged <file>
```

## 查看歷史
```bash
git log --oneline --decorate --graph --all
git show <commit>
```

## 分支
```bash
git branch
git switch -c <branch>
git switch <branch>
git merge <branch>
```

刪除分支：
```bash
git branch -d <branch>
git branch -D <branch>
```

## 遠端（remote）
```bash
git remote -v
git remote add origin <repo_url>
git fetch
git pull
git push -u origin <branch>
```

## 暫存（stash）
```bash
git stash
git stash list
git stash pop
git stash drop
```

## 復原與回退（常用）

### 還原工作區檔案（未加入暫存區）
```bash
git restore <file>
git restore .
```

### 還原暫存區（保留工作區內容）
```bash
git restore --staged <file>
```

### 產生「反向提交」（安全，適合已推上遠端）
```bash
git revert <commit>
```

### 重設分支指標（小心使用）
```bash
git reset --soft <commit>
git reset --mixed <commit>
git reset --hard <commit>
```

## 標籤（tag）
```bash
git tag
git tag -a v1.0.0 -m "release"
git push --tags
```

## .gitignore（最常用）
建立/更新 `.gitignore` 後：
```bash
git add .gitignore
git commit -m "chore: update gitignore"
```

