# Workflow: 容器化应用

## 触发条件
用户说：Docker、容器化、镜像、部署、docker-compose

## 执行步骤

### Step 1: 分析项目
- 前后端是否分离？
- 运行时依赖（Node.js 版本、数据库）
- 静态资源处理方式

### Step 2: 编写 Dockerfile
```dockerfile
# ── Build Stage ──
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# ── Production Stage ──
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./
RUN npm ci --production && npm cache clean --force
USER node
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -q http://localhost:3000/health || exit 1
CMD ["node", "dist/index.js"]
```

### Step 3: 编写 docker-compose.yml
```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=sqlite:///data/app.db
    volumes:
      - app-data:/data
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    restart: unless-stopped

volumes:
  app-data:
```

### Step 4: 创建 .dockerignore
```
node_modules
.git
.env
*.md
.vscode
```

### Step 5: 验证
```bash
docker build -t app .
docker-compose up -d
curl http://localhost:3000/health
```

## 常见陷阱
- **镜像过大**：没用多阶段构建 → 必须用 multi-stage build
- **以 root 运行**：安全风险 → 加 `USER node`
- **缺少健康检查**：K8s/Docker 无法判断服务状态 → 加 `HEALTHCHECK`
- **未忽略 node_modules**：COPY 会把 node_modules 也拷进去 → 加 `.dockerignore`
