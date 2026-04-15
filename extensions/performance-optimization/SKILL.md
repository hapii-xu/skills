---
name: performance-optimization
description: "性能优化技能。掌握前端和后端性能优化方法、性能分析和调优。当需要提升系统性能、解决性能瓶颈时使用此技能。"
scope: personal
---

# 性能优化技能

## 前端优化

### 加载性能
- 代码分割（Code Splitting）和懒加载
- 图片优化（WebP、懒加载、响应式尺寸）
- 资源预加载（preload/prefetch）
- 减少打包体积（Tree Shaking）
- 使用 CDN 加速静态资源

### 渲染性能
- 避免不必要的重渲染（React.memo, useMemo, useCallback）
- 虚拟列表处理大数据量
- 防抖和节流处理频繁事件
- Web Worker 处理密集计算
- requestAnimationFrame 优化动画

### 缓存策略
- 浏览器缓存（Cache-Control, ETag）
- Service Worker 离线缓存
- React Query / SWR 数据缓存
- LocalStorage / IndexedDB 持久化

## 后端优化

### 数据库优化
- 慢查询分析和索引优化
- 避免 N+1 查询问题
- 分页查询替代全量加载
- 读写分离（如适用）
- 连接池配置优化

### API 优化
- 响应压缩（gzip/brotli）
- 批量接口减少请求次数
- GraphQL 按需查询（如适用）
- 合理的缓存策略
- 异步处理耗时操作

### 通用优化
- 并行执行独立任务
- 内存泄漏检测和修复
- 日志级别合理配置
- 资源及时释放

## 性能分析工具
- Chrome DevTools Performance
- React Profiler
- Lighthouse 审计
- 数据库 EXPLAIN 分析
