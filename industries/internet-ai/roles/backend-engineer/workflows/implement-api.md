# Workflow: 实现API端点

## 触发条件
用户说：实现接口、开发API、添加端点、创建路由、开发CRUD

## 前置条件
- [ ] 需求文档或 API 设计说明
- [ ] 数据模型已明确
- [ ] 认证方式已确认

## 执行步骤

### Step 1: 分析需求
1. 确定资源类型（用户、订单、文章等）
2. 列出需要的端点（CRUD + 自定义）
3. 确定请求/响应格式
4. 识别业务规则（验证逻辑、权限控制）

### Step 2: 设计接口
```
POST   /api/users          创建用户
GET    /api/users           获取用户列表（分页）
GET    /api/users/:id       获取用户详情
PUT    /api/users/:id       更新用户
DELETE /api/users/:id       删除用户
```

### Step 3: 创建类型和验证 Schema
```typescript
// src/schemas/user.schema.ts
import { z } from 'zod';

export const createUserSchema = z.object({
  name: z.string().min(2).max(50),
  email: z.string().email(),
  password: z.string().min(8),
});

export type CreateUserInput = z.infer<typeof createUserSchema>;
```

### Step 4: 实现路由和控制器
```typescript
// src/routes/users.ts
import { FastifyInstance } from 'fastify';

export async function userRoutes(app: FastifyInstance) {
  app.post('/api/users', {
    schema: { body: createUserSchema },
  }, async (request, reply) => {
    const input = request.body as CreateUserInput;
    const user = await userService.create(input);
    return { success: true, data: user };
  });
}
```

### Step 5: 实现 Service 层
```typescript
// src/services/user-service.ts
export const userService = {
  async create(input: CreateUserInput) {
    // 1. 检查唯一约束（email）
    // 2. 密码哈希
    // 3. 写入数据库
    // 4. 返回结果（去除敏感字段）
  },
};
```

### Step 6: 编写测试
```typescript
// src/tests/users.test.ts
describe('POST /api/users', () => {
  it('should create user with valid input', async () => { /* ... */ });
  it('should reject duplicate email', async () => { /* ... */ });
  it('should reject invalid input', async () => { /* ... */ });
});
```

## 常见陷阱
- **缺少输入验证**：直接信任请求体 → 必须用 schema 验证
- **忘记错误处理**：数据库操作未 try-catch → 统一错误中间件
- **返回敏感字段**：密码、token 返回给客户端 → 序列化时过滤
- **N+1 查询**：循环中做查询 → 使用 JOIN 或批量查询
- **无分页**：`SELECT *` 返回全量 → 始终使用分页

## 决策分支
```
是否需要认证？
├─ 是 → 添加 auth middleware（preHandler）
└─ 否 → 公开接口

操作复杂度？
├─ 简单 CRUD → 单层 Service
└─ 复杂业务 → 引入 Domain 层 + 事务管理

是否需要缓存？
├─ 读多写少 → Redis 缓存
└─ 写多 → 不缓存，直接查库
```
