---
name: api-design
description: "API 设计技能。掌握 RESTful 设计原则、接口文档规范和 API 版本管理。当需要设计 API 接口、编写接口文档时使用此技能。"
scope: personal
---

# API 设计技能

## RESTful 设计原则

### URL 设计
- 资源名使用复数：`/users`, `/orders`
- 嵌套资源表达关系：`/users/:id/orders`
- 使用 query 参数过滤：`/users?status=active`
- 避免在 URL 中使用动词

### HTTP 方法
- `GET` — 查询资源，不修改状态
- `POST` — 创建资源
- `PUT` — 全量更新资源
- `PATCH` — 部分更新资源
- `DELETE` — 删除资源

### 响应格式
```json
{
  "success": true,
  "data": {},
  "error": null,
  "pagination": { "page": 1, "pageSize": 20, "total": 100 }
}
```

### 错误响应
```json
{
  "success": false,
  "error": "VALIDATION_ERROR",
  "message": "邮箱格式不正确",
  "details": [{ "field": "email", "message": "Invalid email format" }]
}
```

### 状态码使用
- 200: 成功
- 201: 创建成功
- 400: 请求参数错误
- 401: 未认证
- 403: 无权限
- 404: 资源不存在
- 409: 资源冲突
- 422: 验证失败
- 500: 服务器错误

## 版本管理
- URL 版本：`/api/v1/users`
- 向后兼容的修改不升级版本
- 破坏性变更升级大版本
