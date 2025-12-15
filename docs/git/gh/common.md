# gh 常用指令（GitHub CLI）

## 版本與說明
```bash
gh --version
gh help
gh <command> --help
```

## 登入 / 權限
```bash
gh auth login
gh auth status
gh auth logout
```

## Repo

建立 repo：
```bash
gh repo create
gh repo create <name> --public
gh repo create <name> --private
```

複製 repo：
```bash
gh repo clone <owner>/<repo>
```

查看 repo：
```bash
gh repo view
gh repo view <owner>/<repo> --web
```

Fork：
```bash
gh repo fork
```

## PR（Pull Request）

建立 PR：
```bash
gh pr create
gh pr create --fill
gh pr create --draft
```

查看 PR：
```bash
gh pr list
gh pr view <number>
gh pr view <number> --web
```

切到 PR 分支：
```bash
gh pr checkout <number>
```

合併 PR：
```bash
gh pr merge <number>
gh pr merge <number> --merge
gh pr merge <number> --squash
gh pr merge <number> --rebase
```

檢查狀態：
```bash
gh pr status
```

## Issue
```bash
gh issue create
gh issue list
gh issue view <number>
gh issue view <number> --web
gh issue close <number>
```

## Actions（CI）
```bash
gh run list
gh run view <run_id>
gh run watch <run_id>
gh run download <run_id>
```

## Release
```bash
gh release list
gh release view <tag>
gh release create <tag>
```

## 在瀏覽器開啟目前 repo
```bash
gh repo view --web
```

