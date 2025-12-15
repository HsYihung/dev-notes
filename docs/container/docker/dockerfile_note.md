# Dockerfile 寫法

## 常用指令速查

### 基本結構
```dockerfile
FROM alpine:3.20
WORKDIR /app
COPY . .
RUN echo "build"
CMD ["sh"]
```

### 常見指令

#### `FROM`：選擇基底映像
- 決定你的 image 以哪個 base image 為起點（OS/Runtime/工具鏈）
- 建議固定 tag（例如 `alpine:3.20`、`node:20-alpine`），或用 digest 做到完全可重現
- 可搭配 `AS` 做 multi-stage build
```dockerfile
FROM node:20-alpine AS build
FROM alpine:3.20
```

#### `WORKDIR`：設定工作目錄
- 之後的 `RUN` / `COPY` / `CMD` 會以此為相對路徑基準
- 目錄不存在會自動建立（等同於建置時 mkdir）
```dockerfile
WORKDIR /app
COPY . .
```

#### `COPY` / `ADD`：拷貝檔案
- 多數情況使用 `COPY`（行為單純、可預期）
- `ADD` 會多一些「魔法」：可解壓縮本地 tar、可抓 URL（不建議用來下載依賴）
- 常見需求：用 `--chown` 設定檔案擁有者（避免 runtime 權限問題）
```dockerfile
COPY package*.json ./
COPY --chown=node:node . .
```

#### `RUN`：建置階段執行指令（會產生 layer）
- 每個 `RUN` 會形成一層（layer）；把相關指令串成一個 `RUN` 可減少 layer 並改善快取命中
- 盡量用 exec form 的 shell（或明確指定 shell），並在同一層清理暫存避免 image 變大
```dockerfile
RUN apk add --no-cache curl
# Debian/Ubuntu 常見（示意）
# RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
```

#### `ENV`：設定環境變數
- 會寫進 image，container 啟動後預設就存在
- 常用來設定 runtime 行為（例如時區、語系、app mode）
```dockerfile
ENV NODE_ENV=production
ENV PORT=3000
```

#### `EXPOSE`：文件化要使用的 port
- 只是「宣告/文件化」容器內部會用到哪些 port，不會自動對外開
- 對外開放要靠 `docker run -p` 或 compose `ports`
```dockerfile
EXPOSE 3000
```

#### `ENTRYPOINT` / `CMD`：容器啟動行為
- `CMD`：預設要跑的命令/參數（較容易被 `docker run ... <cmd>` 覆蓋）
- `ENTRYPOINT`：容器主要執行檔（常用於固定主程式，再用 `CMD` 當預設參數）
- 建議使用 exec form（JSON array）避免訊號轉發/字串解析問題
```dockerfile
ENTRYPOINT ["node"]
CMD ["server.js"]
```

## 建議做法

### 1) 使用 `.dockerignore` 減少內容
建立 `.dockerignore`（範例）：
```gitignore
.git
node_modules
dist
*.log
```

### 2) 分層快取：先拷貝依賴描述檔再安裝
Node.js（示意）：
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
CMD ["npm","start"]
```

### 3) Multi-stage build（減少最終映像大小）
```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```

## 常用建置指令
```bash
docker build -t myapp:dev .
docker run --rm -p 8080:80 myapp:dev
```

## 補充：其他常見 Dockerfile 指令

#### `ARG`：建置參數（只在 build 期間存在）
- 用來控制建置流程（例如切換版本、指定 private registry）
- `ARG` 不會自動存在於 runtime；需要的話可搭配 `ENV`
```dockerfile
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-alpine
ARG APP_VERSION
ENV APP_VERSION=${APP_VERSION}
```

#### `LABEL`：加入 metadata
- 用於標記維護者、版本、source 等資訊，方便查詢與自動化
```dockerfile
LABEL org.opencontainers.image.source="https://github.com/owner/repo"
LABEL org.opencontainers.image.version="1.2.3"
```

#### `USER`：切換執行使用者
- 建議避免用 root 跑應用程式（降低風險、避免檔案權限問題）
```dockerfile
RUN addgroup -S app && adduser -S app -G app
USER app
```

#### `VOLUME`：宣告掛載點
- 宣告某路徑預期作為持久化資料/外部掛載（實際掛載仍由 run/compose 決定）
```dockerfile
VOLUME ["/data"]
```

#### `HEALTHCHECK`：健康檢查
- 讓 orchestrator/平台更容易判斷容器是否健康
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -qO- http://127.0.0.1:3000/health || exit 1
```

#### `SHELL`：指定 `RUN` 使用的 shell
- 需要特定 shell 行為或在 Windows container 時特別有用
```dockerfile
SHELL ["/bin/sh", "-c"]
```

#### `ONBUILD`：給「被當作 base image 的 image」使用
- 當別人的 Dockerfile `FROM` 你的 image 時，自動觸發某些指令（使用上要謹慎）
```dockerfile
ONBUILD COPY . /src
```
