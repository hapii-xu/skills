# Workflow: 搭建CI/CD流水线

## 触发条件
用户说：CI/CD、流水线、自动化部署、GitHub Actions、自动构建

## 执行步骤

### Step 1: 确认项目结构
- 前端构建命令（如 `npm run build`）
- 后端启动命令（如 `npm start`）
- 测试命令（如 `npm test`）
- 部署目标（Docker / VPS / 云平台）

### Step 2: 设计流水线
```
PR 流水线：
  lint → type-check → test → build（验证）

Deploy 流水线（main merge 触发）：
  build → docker build → deploy to staging → smoke test → deploy to prod
```

### Step 3: 创建 GitHub Actions 配置
```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm test
      - run: npm run build
```

### Step 4: 配置环境变量
创建 `.env.example`：
```
# 应用
PORT=3000
NODE_ENV=production

# 数据库
DATABASE_URL=sqlite:///data/app.db

# 认证
JWT_SECRET=your-secret-here
```

在 GitHub Settings → Secrets 中配置实际值。

### Step 5: 验证
- [ ] PR 创建时自动运行 CI
- [ ] 测试失败时阻止合并
- [ ] main 分支合并时自动部署
- [ ] 部署失败有通知
