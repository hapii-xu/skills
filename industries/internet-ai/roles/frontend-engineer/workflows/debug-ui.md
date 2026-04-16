# Workflow: UI 调试

## 触发条件
用户说：UI问题、样式错误、显示异常、布局错位、Safari/移动端有问题

## 执行步骤

### Step 1: 复现问题
1. 确认问题描述（哪个页面？哪个元素？什么情况下出现？）
2. 打开相关文件，定位组件
3. 检查相关的 HTML 结构和 CSS 类名

### Step 2: 隔离问题
按以下顺序排查：

**布局问题**：
```
是否是 flex/grid 容器问题？
├─ 检查 display: flex / grid 是否设置
├─ 检查 flex-direction / justify-content / align-items
├─ 检查 gap / padding / margin
└─ 检查是否有 overflow: hidden 导致内容被裁剪
```

**样式问题**：
```
样式是否生效？
├─ 检查 class 名是否正确
├─ 检查 Tailwind 断点前缀（sm:/md:/lg:）
├─ 检查是否有更高优先级的样式覆盖
└─ 检查 CSS specificity 冲突
```

**响应式问题**：
```
只在某个尺寸出问题？
├─ 检查断点是否遗漏
├─ 检查固定宽度/高度值（应改为百分比或 max-w）
├─ 检查图片是否有 max-width: 100%
└─ 检查 viewport meta 标签
```

### Step 3: 修复
- 确认修复方案（最小改动原则）
- 实现 fix
- 确保不引入新问题

### Step 4: 验证
- 原问题是否修复
- 其他尺寸是否正常
- 相邻组件是否受影响

## 常见问题速查

| 问题 | 常见原因 | 修复 |
|------|---------|------|
| 元素不换行 | flex 容器缺少 `flex-wrap` | 加 `flex-wrap` |
| 文字溢出 | 容器无 `overflow` | 加 `overflow-hidden` + `text-ellipsis` |
| 布局跳动 | 无固定高度/skeleton | 加 `min-h-[Xpx]` 或 skeleton |
| 移动端太小 | 触摸目标 < 44px | 增大 padding |
| z-index 无效 | 未创建 stacking context | 检查 `position` 是否设置 |
| 图片变形 | 无 `object-fit` | 加 `object-cover` |

## 输出格式
```markdown
### Bug 修复：[问题描述]

**根因**：[一句话说明]
**修复**：[修改了什么]
**影响范围**：[是否影响其他组件]
**验证**：[修复后如何验证]
```
