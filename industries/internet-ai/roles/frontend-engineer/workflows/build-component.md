# Workflow: 构建React组件

## 触发条件
用户说：创建组件、实现XX页面、添加XX表单、开发XX功能、构建XX卡片

## 前置条件（缺失则先询问）
- [ ] 需求描述或设计参考
- [ ] 数据来源明确（props / API / context）
- [ ] 需要处理的交互状态

## 执行步骤

### Step 1: 分析需求（快速完成）
1. 从需求文本中提取组件用途
2. 列出数据需求（输入 props、API 调用）
3. 列出交互需求（点击、表单提交、拖拽）
4. 识别需要处理的 UI 状态

### Step 2: 设计组件接口
```tsx
interface ComponentNameProps {
  /** 属性描述 */
  propName: PropType;
}

// 如果有复杂状态，定义 State 类型
type ComponentState = {
  isLoading: boolean;
  data: DataType[];
  error: string | null;
};
```

**决策点**：
- 数据来自 props？→ 简单展示组件
- 数据需要 API 获取？→ 抽取自定义 Hook
- 交互复杂？→ 考虑 useReducer 替代 useState

### Step 3: 创建文件结构
```
src/components/ComponentName/
├── ComponentName.tsx     # 主组件
├── index.ts              # re-export
└── types.ts              # 类型定义（如果复杂）
```

### Step 4: 实现组件（按固定顺序）
```tsx
// 1. 状态声明
const [state, setState] = useState(initialValue);

// 2. 派生状态
const computed = useMemo(() => derive(state), [state]);

// 3. 副作用
useEffect(() => { /* 数据获取等 */ }, [dep]);

// 4. 事件处理
const handleClick = () => { /* ... */ };

// 5. 渲染
return (
  <div className="...">
    {/* JSX */}
  </div>
);
```

### Step 5: 处理所有状态
| 状态 | 实现 |
|------|------|
| Loading | `<Skeleton />` 或 `<Spinner />` |
| Empty | 空状态插图 + 引导文案 |
| Error | 错误信息 + 重试按钮 |
| Normal | 正常渲染 |

### Step 6: 验证交付
运行 SKILL.md Section 6 质量清单，按 Section 7 格式输出。

## 常见陷阱
- **上帝组件**：超过 200 行就拆分，抽取子组件或自定义 Hook
- **忘记空状态**：列表数据为空时页面全白
- **数组 index 作 key**：动态列表用唯一 ID 作 key
- **内联对象/函数**：在渲染中创建新引用导致不必要重渲染
- **useEffect 缺少依赖**：ESLint exhaustive-deps 规则

## 决策分支
```
数据来源？
├─ Props only → 简单展示组件，无 useEffect
└─ API 调用 → 抽取 useXxxData 自定义 Hook

交互复杂度？
├─ 简单（1-3个状态）→ useState
└─ 复杂（状态间有依赖）→ useReducer

跨页面复用？
├─ 否 → 放在 feature 目录
└─ 是 → 放在 components/shared/
```
