# 介绍
1. php-multi-versions 👉 需要 Nginx + 多 PHP 版本 + MySQL
2. hyperf 👉 需要 Redis + RabbitMQ + Hyperf
3. 希望管理方式清晰，后期可以扩展

📌 最佳实践：使用 docker-compose 的多 compose.yml 管理独立项目，同时共享通用服务

* ✅ 两个项目（php-multi-versions 和 hyperf）可以独立管理，但 共享 Redis、RabbitMQ、MySQL 等基础服务
* ✅ 每个项目都可以独立运行，也可以一起运行
* ✅ 后期如果需要增加更多微服务（如 Kafka、Elasticsearch），只需扩展通用服务

# 独立管理项目，通用基础服务
## 📌 目录结构
```plaintext
docker/
  ├── services/         # 📌 共享的基础服务（Redis、RabbitMQ、MySQL）
  │   ├── docker-compose.services.yml
  │   ├── mysql-init/   # MySQL 初始化脚本
  ├── php-multi-versions/   # 📌 PHP 多版本项目
  │   ├── docker-compose.yml
  │   ├── www/          # PHP 项目代码
  │   ├── nginx/        # Nginx 配置
  ├── hyperf/           # 📌 Hyperf 项目
  │   ├── docker-compose.yml
  │   ├── src/          # Hyperf 代码
  │── php-multi-versions-v1/   # 📌 PHP 多版本项目,非共享基础服务
```
📌 这样，每个项目的 docker-compose.yml 只包含自己的服务，但共享 services 里的 Redis、RabbitMQ、MySQL。

## ✅ 运行方式
### 1️⃣ 先启动通用服务（MySQL、Redis、RabbitMQ）
```bash
cd docker/services
docker-compose -f docker-compose.services.yml up -d
```



# Q&A
## network backend declared as external, but could not be found
🔹 1️⃣ 先手动创建 backend 网络
```bash
docker network create backend
```
📌 这条命令会创建一个 bridge 网络，供多个 docker-compose 文件共享。

🔹 2️⃣ 确保 docker network ls 里有 backend
运行：
```bash
docker network ls
```
✅ 你应该看到类似的输出：
```sql
NETWORK ID          NAME                DRIVER              SCOPE
b3b5c4f897e9        backend             bridge              local
```

✅ 3️⃣ 重新启动 docker-compose
重新启动 php-multi-versions
```bash
cd docker/php-multi-versions
docker-compose up -d
```

重新启动 hyperf
```bash
cd docker/hyperf
docker-compose up -d
```
📌 现在 docker-compose 就不会报错了！

## service "hyperf_webstore" depends on undefined service "redis": invalid compose project