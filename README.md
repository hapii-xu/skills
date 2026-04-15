# Skills Hub

Feather AI 员工技能模板仓库。

## 目录结构

```
skills-hub/
├── roles/                        # 角色技能模板（按行业分类）
│   └── internet-ai/              # 互联网/AI 行业
│       ├── pm.md                 # 产品经理
│       ├── frontend.md           # 前端工程师
│       ├── backend.md            # 后端工程师
│       ├── tester.md             # 测试工程师
│       ├── designer.md           # 设计师
│       ├── devops.md             # 运维工程师
│       ├── fullstack.md          # 全栈工程师
│       ├── developer.md          # 开发工程师
│       └── assistant.md          # AI 助手
├── extensions/                   # 扩展技能（个人技能）
│   ├── code-review/SKILL.md
│   ├── api-design/SKILL.md
│   ├── database-design/SKILL.md
│   ├── testing-strategy/SKILL.md
│   ├── performance-optimization/SKILL.md
│   └── security-audit/SKILL.md
├── team-templates/               # 团队模板（按行业定义项目类型和最小团队）
│   ├── internet-ai.json
│   ├── lawyer.json
│   └── teacher.json
└── collaboration-interfaces/     # 协作接口定义（角色间依赖和产出关系）
    └── internet-ai.json
```

## 模板格式

### 角色技能 (roles/)

Markdown 文件，包含 YAML frontmatter：

```yaml
---
name: product-manager
description: "角色描述"
industry: internet-ai
category: 产品
position: 产品经理
tech_stack:
  - Markdown
default_extensions:
  - code-review
---

# 角色名称 — Work Skill
[角色定义、工作规范、输出风格]
```

### 扩展技能 (extensions/)

每个技能一个目录，包含 `SKILL.md`：

```yaml
---
name: code-review
description: "技能描述"
scope: personal
---

# 技能内容
```
