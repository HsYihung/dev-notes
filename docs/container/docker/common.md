# Docker 常用指令

## 版本 / 狀態
```bash
docker --version
docker info
docker system df
```

## Image
```bash
docker images
docker pull <image>:<tag>
docker build -t <name>:<tag> .
docker rmi <image>
```

清理（小心）：
```bash
docker image prune
docker system prune
```

## Container
```bash
docker ps
docker ps -a
docker run --rm <image>
docker run -it --rm <image> sh
docker logs -f <container>
docker exec -it <container> sh
docker stop <container>
docker rm <container>
```

常用 run 參數：
```bash
docker run -d --name <name> -p 8080:80 <image>
docker run --rm -e KEY=value <image>
docker run --rm -v "$PWD":/app <image>
```

## Volume / Network
```bash
docker volume ls
docker volume create <name>
docker volume rm <name>

docker network ls
docker network create <name>
docker network rm <name>
```

