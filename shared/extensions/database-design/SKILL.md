---
name: database-design
description: "数据库设计技能。掌握关系型数据库设计范式、索引优化和数据建模。当需要设计数据库表结构、优化查询性能时使用此技能。"
scope: personal
---

# 数据库设计技能

## 设计原则

### 范式规范
- 1NF：字段不可再分
- 2NF：非主键字段完全依赖主键
- 3NF：非主键字段不传递依赖主键
- 适度反范式：为了查询性能可适当冗余

### 表设计规范
- 表名使用 snake_case 复数：`user_profiles`
- 字段名使用 snake_case：`created_at`
- 每个表必须有主键 `id`
- 包含 `created_at` 和 `updated_at` 时间戳
- 使用 `NOT NULL` 约束明确字段必填
- 外键关系明确，使用 `FOREIGN KEY` 约束

### 索引策略
- 主键自动创建索引
- WHERE 条件频繁查询的字段建索引
- JOIN 关联字段建索引
- 组合索引注意字段顺序（选择性高的在前）
- 避免过度索引（影响写入性能）

### 数据类型选择
- 字符串：VARCHAR(n) / TEXT
- 数字：INT / BIGINT / DECIMAL
- 时间：ISO 8601 字符串 / TIMESTAMP
- 布尔：INTEGER (0/1)（SQLite 兼容）
- JSON：TEXT + 应用层解析

### 迁移脚本规范
- 每次变更一个迁移文件
- 包含 UP 和 DOWN（回滚）
- 不可修改已发布的迁移
- 数据迁移和结构迁移分离
