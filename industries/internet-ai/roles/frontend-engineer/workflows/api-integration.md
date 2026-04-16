# Workflow: API 对接

## 触发条件
用户说：对接API、获取数据、调接口、连接后端、加载数据

## 前置条件
- [ ] API 端点路径和文档
- [ ] 请求/响应格式
- [ ] 认证方式

## 执行步骤

### Step 1: 确认接口契约
从后端文档或沟通中获取：
- 端点：`GET /api/resource`
- 请求参数：query params / request body
- 响应格式：`{ success, data, error }`
- 认证：Header / Cookie

**如果文档不存在**：主动给后端发消息说明需要的接口格式。

### Step 2: 定义类型
```tsx
// src/types/api.ts
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: { code: string; message: string };
}

interface User {
  id: string;
  name: string;
  email: string;
}
```

### Step 3: 封装数据获取 Hook
```tsx
// src/hooks/useUsers.ts
export function useUsers() {
  const [users, setUsers] = useState<User[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    let cancelled = false;
    setIsLoading(true);

    fetch('/api/users')
      .then(res => res.json())
      .then(data => {
        if (!cancelled) {
          setUsers(data.data ?? []);
          setError(null);
        }
      })
      .catch(err => {
        if (!cancelled) setError(err.message);
      })
      .finally(() => {
        if (!cancelled) setIsLoading(false);
      });

    return () => { cancelled = true; };
  }, []);

  return { users, isLoading, error };
}
```

### Step 4: 处理所有 UI 状态
```tsx
function UserList() {
  const { users, isLoading, error } = useUsers();

  if (isLoading) return <UserListSkeleton />;
  if (error) return <ErrorState message={error} onRetry={() => window.location.reload()} />;
  if (users.length === 0) return <EmptyState description="暂无用户数据" />;

  return (
    <ul>
      {users.map(user => <UserCard key={user.id} user={user} />)}
    </ul>
  );
}
```

### Step 5: 添加请求层封装（可选）
如果项目有多个 API 调用，创建统一请求层：
```tsx
// src/lib/api-client.ts
export async function apiClient<T>(url: string, options?: RequestInit): Promise<T> {
  const res = await fetch(url, {
    headers: {
      'Content-Type': 'application/json',
      ...options?.headers,
    },
    ...options,
  });

  if (!res.ok) {
    throw new Error(`API Error: ${res.status}`);
  }

  return res.json();
}
```

### Step 6: 验证
- Loading 状态显示骨架屏（无布局跳动）
- 空数据有提示
- 网络错误有重试按钮
- 数据正确渲染

## 常见陷阱
- **竞态条件**：组件卸载后 setState → 使用 `cancelled` 标志
- **无 Loading 态**：数据加载中页面空白
- **无错误处理**：网络异常时页面崩溃
- **过度请求**：每次渲染都触发 fetch → 用 useEffect + deps 控制
- **硬编码 URL**：API 地址应配置化

## 决策分支
```
请求频率？
├─ 一次性加载 → useEffect + fetch
└─ 频繁触发 → 考虑 SWR/React Query

数据是否共享？
├─ 组件内 → useState
└─ 跨组件 → Context 或状态管理库

是否需要缓存？
├─ 否 → 简单 fetch
└─ 是 → 引入 SWR 或自定义缓存层
```
