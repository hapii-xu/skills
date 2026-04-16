# Workflow: 数据库设计

## 触发条件
用户说：设计数据库、建表、数据模型、表结构、ER图

## 执行步骤

### Step 1: 实体识别
从需求文档中提取核心实体：
1. 列出所有"名词"（用户、订单、商品）
2. 识别实体间关系（一对一、一对多、多对多）
3. 确定每个实体的核心属性

### Step 2: 设计表结构
```sql
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT NOT NULL UNIQUE,
  name TEXT NOT NULL,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL DEFAULT 'user',
  created_at TEXT NOT NULL DEFAULT (datetime('now')),
  updated_at TEXT NOT NULL DEFAULT (datetime('now'))
);
```

**每张表必须包含**：
- `id` — 主键（TEXT 或 INTEGER）
- `created_at` — 创建时间
- `updated_at` — 更新时间

### Step 3: 关系设计
```
一对多：在"多"的一方添加外键
  users(1) → posts(N): posts.user_id → users.id

多对多：创建中间表
  users(N) ↔ roles(N): user_roles(user_id, role_id)
```

### Step 4: 索引规划
```sql
-- 唯一索引（业务唯一性）
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- 查询索引（高频查询条件）
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_created_at ON posts(created_at DESC);
```

### Step 5: 编写迁移脚本
```sql
-- migrations/001_create_users.sql
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  email TEXT NOT NULL UNIQUE,
  name TEXT NOT NULL,
  password_hash TEXT NOT NULL,
  created_at TEXT NOT NULL DEFAULT (datetime('now')),
  updated_at TEXT NOT NULL DEFAULT (datetime('now'))
);

CREATE UNIQUE INDEX idx_users_email ON users(email);
```

### Step 6: 验证
- [ ] 所有实体和关系已覆盖
- [ ] 每张表有 id/created_at/updated_at
- [ ] 外键关系正确
- [ ] 唯一约束已设置
- [ ] 高频查询字段有索引

## 常见陷阱
- **过度规范化**：5 层 JOIN 查询 → 适当反范式化
- **无索引**：大数据量表查询慢 → 为查询条件加索引
- **硬删除**：直接 DELETE → 考虑软删除（deleted_at 字段）
- **缺少时间戳**：无法追踪记录变更 → 必须有 created_at/updated_at

## 决策分支
```
数据规模？
├─ 小型（< 10万行）→ SQLite 足够
└─ 大型（> 10万行）→ 考虑 PostgreSQL

读写比？
├─ 读多写少 → 加索引 + 可考虑缓存
└─ 写多 → 关注事务和锁策略

是否需要全文搜索？
├─ 否 → 普通索引即可
└─ 是 → SQLite FTS5 或 Elasticsearch
```
