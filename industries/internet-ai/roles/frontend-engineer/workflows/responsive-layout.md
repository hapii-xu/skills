# Workflow: 响应式布局

## 触发条件
用户说：响应式、移动端适配、屏幕适配、手机上显示、平板适配

## TailwindCSS 断点参考
```
默认：0-639px（移动端，不写前缀）
sm:   640px+  — 大屏手机横屏
md:   768px+  — 平板竖屏
lg:   1024px+ — 平板横屏/小笔记本
xl:   1280px+ — 桌面
2xl:  1536px+ — 大屏桌面
```

## 移动优先原则
先写移动端样式（无前缀），再逐步增加断点覆盖大屏：
```tsx
<div className="
  flex flex-col gap-4     /* 移动端：纵向排列 */
  md:flex-row md:gap-6    /* 平板+：横向排列 */
  lg:gap-8                /* 桌面：更宽间距 */
">
```

## 常见响应式模式

### 导航栏
```
Mobile: 汉堡菜单 → 侧边栏
Desktop: 水平导航栏
```

### 卡片网格
```
Mobile:  grid-cols-1
Tablet:  md:grid-cols-2
Desktop: lg:grid-cols-3
```

### 侧边栏布局
```
Mobile: 侧边栏隐藏，通过按钮展开（overlay）
Desktop: lg:flex → 侧边栏常驻 + 主内容区
```

### 表格
```
Mobile: 每行变为卡片式布局（隐藏 thead，每列变为 label: value）
Desktop: 标准表格
```

## 执行步骤

1. **确认断点需求**：需要适配哪些尺寸？
2. **先写移动端**：最小屏幕的基础布局
3. **逐步增加断点**：md → lg → xl
4. **处理图片**：`max-w-full h-auto` 或 `object-cover`
5. **处理文字**：`text-sm md:text-base lg:text-lg`
6. **处理间距**：`p-4 md:p-6 lg:p-8`
7. **验证**：检查 375px / 768px / 1280px 三个关键宽度

## 质量检查
- [ ] 375px（iPhone SE）显示正常
- [ ] 768px（iPad）显示正常
- [ ] 1280px（桌面）显示正常
- [ ] 触摸目标 >= 44px
- [ ] 文字不会溢出容器
- [ ] 图片自适应不变形
