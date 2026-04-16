---
name: testing-strategy
description: "测试策略技能。掌握测试金字塔、测试最佳实践和自动化测试框架。当需要制定测试方案、编写测试代码时使用此技能。"
scope: personal
---

# 测试策略技能

## 测试金字塔

```
     /\
    /  \        E2E 测试（少量）
   /    \       覆盖核心业务流程
  /------\
 /        \     集成测试（适量）
/          \    覆盖 API 和模块交互
/------------\
  单元测试（大量）
  覆盖函数、工具方法、业务逻辑
```

### 单元测试
- 覆盖率目标 >= 70%
- 测试纯函数和工具方法
- Mock 外部依赖（API、数据库）
- 快速执行，秒级完成

### 集成测试
- 测试 API 接口完整流程
- 使用真实或内存数据库
- 验证请求/响应格式
- 测试错误处理和边界情况

### E2E 测试
- 覆盖核心业务流程
- 使用 Page Object 模式
- 独立的测试环境和数据
- 可在 CI 中运行

## 测试编写原则

### AAA 模式
```typescript
test('should calculate total price', () => {
  // Arrange
  const items = [{ price: 10, quantity: 2 }, { price: 5, quantity: 3 }];
  // Act
  const total = calculateTotal(items);
  // Assert
  expect(total).toBe(35);
});
```

### 命名规范
- 使用 `should_xxx_when_xxx` 格式
- 测试文件：`xxx.test.ts` 或 `xxx.spec.ts`
- 测试目录与源码目录镜像

### Mock 原则
- 只 Mock 外部依赖，不 Mock 被测模块
- Mock 数据贴近真实场景
- 测试完成后清理 Mock 状态

## 测试数据管理
- 使用 Factory 函数生成测试数据
- 避免硬编码测试数据
- 敏感数据使用虚拟值
