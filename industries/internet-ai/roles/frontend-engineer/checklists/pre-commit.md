# Reference: React 高级模式

> 当任务涉及复合组件、自定义 Hook、高级状态管理、性能优化时参考。

## 1. 自定义 Hook 模式

### 数据获取 Hook
```tsx
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<string | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    let cancelled = false;
    setIsLoading(true);

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`${res.status}`);
        return res.json();
      })
      .then(json => { if (!cancelled) setData(json); })
      .catch(err => { if (!cancelled) setError(err.message); })
      .finally(() => { if (!cancelled) setIsLoading(false); });

    return () => { cancelled = true; };
  }, [url]);

  return { data, error, isLoading };
}
```

### 表单状态 Hook
```tsx
function useForm<T extends Record<string, unknown>>(initial: T) {
  const [values, setValues] = useState(initial);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});

  const setValue = (key: keyof T, value: T[keyof T]) => {
    setValues(prev => ({ ...prev, [key]: value }));
    setErrors(prev => ({ ...prev, [key]: undefined }));
  };

  const reset = () => {
    setValues(initial);
    setErrors({});
  };

  return { values, errors, setValue, setErrors, reset };
}
```

## 2. 状态管理决策

```
状态范围？
├─ 组件内 → useState / useReducer
├─ 父子传递 → Props + Callback
├─ 跨层级 → Context
├─ 全局共享 → Zustand / Redux
└─ 服务端状态 → SWR / React Query

状态复杂度？
├─ 独立值 → useState
├─ 相互依赖 → useReducer
└─ 异步副作用 → 自定义 Hook + useEffect
```

## 3. 性能优化模式

### React.memo + useMemo + useCallback
```tsx
// 纯展示组件用 memo 避免不必要重渲染
const ExpensiveList = React.memo(function ExpensiveList({ items }: Props) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
});

// 父组件中用 useMemo 缓存计算结果
const sortedItems = useMemo(
  () => [...items].sort((a, b) => a.name.localeCompare(b.name)),
  [items]
);

// 传递给子组件的回调用 useCallback
const handleClick = useCallback((id: string) => {
  setSelected(id);
}, []);
```

### 懒加载
```tsx
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

## 4. 组件组合模式

### Compound Components
```tsx
// Tabs 组件用 Context 共享状态
function Tabs({ children, defaultIndex = 0 }) {
  const [activeIndex, setActiveIndex] = useState(defaultIndex);
  return (
    <TabsContext.Provider value={{ activeIndex, setActiveIndex }}>
      {children}
    </TabsContext.Provider>
  );
}

Tabs.List = function List({ children }) {
  return <div role="tablist">{children}</div>;
};

Tabs.Panel = function Panel({ index, children }) {
  const { activeIndex } = useContext(TabsContext);
  return activeIndex === index ? <div>{children}</div> : null;
};

// 使用
<Tabs>
  <Tabs.List>
    <button onClick={() => setActive(0)}>Tab 1</button>
  </Tabs.List>
  <Tabs.Panel index={0}>Content 1</Tabs.Panel>
</Tabs>
```
