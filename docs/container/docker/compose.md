# Docker Compose

以 `docker compose`（v2）為主。

## 常用指令
```bash
docker compose version
docker compose up -d
docker compose down
docker compose ps
docker compose logs -f
docker compose exec <service> sh
docker compose build
docker compose pull
```

## 最小範例（`compose.yaml`）
```yaml
services:
  app:
    image: nginx:alpine
    ports:
      - "8080:80"
```

啟動：
```bash
docker compose up -d
```

## 常用寫法

環境變數：
```yaml
services:
  app:
    image: myapp:dev
    environment:
      - NODE_ENV=development
```

掛載 volume：
```yaml
services:
  app:
    image: myapp:dev
    volumes:
      - ./:/app
```

## 配置範例：前後端實戰（`compose.yaml`）

這份範例偏向「更接近實際使用」：前端（Nginx）+ 後端（API）+ DB/Redis，並示範 `build`/`image`、`volumes`、`networks`、`depends_on`、`healthcheck` 等常用配置。
```yaml
name: myapp

services:
  web:
    # image 指定方式：
    # - tag：nginx:1.27-alpine
    # - digest：nginx@sha256:...
    image: ${WEB_IMAGE:-nginx:1.27-alpine}
    # ports：把「宿主機:容器」port 對映起來（對外提供服務）
    ports:
      - "8080:80"
    # depends_on：決定啟動順序/條件（不等同於「服務真的可用」，建議搭配 healthcheck）
    depends_on:
      api:
        condition: service_healthy
    # volumes：掛載設定檔到容器（read_only 避免容器改寫）
    volumes:
      - type: bind
        source: ./frontend/nginx.conf
        target: /etc/nginx/conf.d/default.conf
        read_only: true
    # networks：指定加入哪些 network（可分 public/internal 控制可見性）
    networks:
      - public

  api:
    # image 指定方式：
    # - image + tag : my-api:last
    # - registry + tag：ghcr.io/owner/my-api:1.2.3
    # - digest：ghcr.io/owner/my-api@sha256:...
    #
    # 同時指定 image + build：代表要「build 後順便打 tag 成 image 名稱」
    image: ${API_IMAGE:-ghcr.io/owner/my-api:dev}
    # build：從 Dockerfile 建置 image
    build:
      # context：build 會用到的檔案根目錄（相對於 compose.yaml）
      context: ./backend
      # dockerfile：指定 Dockerfile 路徑（相對於 context）
      dockerfile: Dockerfile
      # target：multi-stage build 的 stage 名稱（例如 production）
      target: production
      # args：傳給 Dockerfile 的 ARG（建置參數）
      args:
        NODE_VERSION: "20"
    # environment：容器 runtime 的環境變數（優先權通常高於 env_file）
    environment:
      NODE_ENV: production
      PORT: "3000"
      DATABASE_URL: postgresql://postgres:postgres@db:5432/app
      REDIS_URL: redis://redis:6379
    # env_file：從檔案載入環境變數（常放敏感資訊，注意不要提交到 repo）
    env_file:
      - ./backend/.env
    # ports：對外開放 API（宿主機 8081 -> 容器 3000）
    ports:
      - "8081:3000"
    # expose：只在 compose network 內部暴露（不會對外開）
    expose:
      - "3000"
    # volumes：常見做法是 bind 掛載原始碼方便開發；node_modules 用 named volume 避免被 host 覆蓋
    volumes:
      - type: bind
        source: ./backend
        target: /app
      - type: volume
        source: api_node_modules
        target: /app/node_modules
    # depends_on：搭配 db healthcheck，確保 API 在 DB ready 後再啟動
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    # healthcheck：讓 compose/平台判斷容器健康狀態（可用於 depends_on 條件）
    healthcheck:
      test: ["CMD-SHELL", "wget -qO- http://127.0.0.1:3000/health || exit 1"]
      interval: 10s
      timeout: 3s
      retries: 5
      start_period: 20s
    # restart：非預期退出時自動重啟（開發時可視需求關閉）
    restart: unless-stopped
    # pull_policy：何時拉取 image（常用 if_not_present / always）
    pull_policy: if_not_present
    # logging：限制 log 檔大小，避免佔滿磁碟
    logging:
      driver: json-file
      options:
        max-size: 10m
        max-file: "3"
    # networks：同時加入 public/internal，讓 web 可以連到 api，且 api 可以連到 db/redis
    networks:
      - public
      - internal

  db:
    image: postgres:16-alpine
    # environment：初始化 DB 的預設帳密/資料庫名稱（範例用，正式環境請改成安全值）
    environment:
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: app
    # volumes：持久化資料（避免容器刪除後資料不見）
    volumes:
      - db_data:/var/lib/postgresql/data
    # 需要從 host 連線除錯再打開（否則可不暴露）
    ports:
      - "5432:5432"
    # healthcheck：讓 api 可以用 service_healthy 條件等待 DB ready
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d app"]
      interval: 10s
      timeout: 3s
      retries: 10
      start_period: 20s
    restart: unless-stopped
    networks:
      - internal

  redis:
    image: redis:7-alpine
    # volumes：持久化資料（依需求可省略）
    volumes:
      - redis_data:/data
    restart: unless-stopped
    networks:
      - internal

networks:
  # public：對外服務的 network（web/api 會在這裡互通）
  public: {}
  # internal：內網 network（設為 internal，避免被外部直接連到）
  internal:
    internal: true

volumes:
  # named volumes：由 Docker 管理的持久化磁碟
  db_data: {}
  redis_data: {}
  api_node_modules: {}
```

小提醒：
- 想固定版本避免 tag 漂移：把 `WEB_IMAGE` / `API_IMAGE` 改成 digest（`@sha256:...`）
- 想只用預建 image：移除 `api.build`（只留 `api.image`），再跑 `docker compose pull && docker compose up -d`
