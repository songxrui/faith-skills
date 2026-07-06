---
name: faith-skill-deploy
description: "Deploy a skill to production — syntax check, frontmatter validation, dependency resolution, trigger-route testing, and GitHub push. Use when the user says \`"部署skill\`", \`"发布skill\`", \`"deploy skill\`", or has a completed skill ready for release. Not for skill creation (→ faith-skill-factory) or evaluation (→ faith-skill-solution)."
disable-model-invocation: true
argument-hint: "[skill-path]"
version: "1.3.0 | R1.3: mattpocock description alignment | 2026-07-06 | model: agnostic"
---
# faith-skill-deploy — Skill 部署引擎

> **deploy** — validate, scaffold, push. Deploy only; never modify the source SKILL.md.

## 前置条件

执行前检查，任一不满足则中止并输出失败项：

- [ ] 源 skill 存在: `Test-Path <skill_dir>/SKILL.md`
- [ ] GitHub CLI 已认证: `gh auth status`
- [ ] Git 已配置 user.name / user.email

## 部署流程（6 步）

### Step 1：读取元数据

解析 frontmatter 的 name 和 description。提取版本号。输出 skill 摘要。

### Step 2：生成仓库名

规则：`skill-{name}`。去重前缀（已有 `skill-` 则不再加）。检查是否已存在 `gh repo view KKKKhazix/<repo-name>`。冲突则询问覆盖/跳过/重命名。

### Step 3：构建骨架

```
<repo-name>/
├── SKILL.md              # 原 skill 文件（不修改）
├── README.md             # 自动生成
├── LICENSE               # MIT
├── .gitignore
└── .github/workflows/validate.yml
```

validate.yml 检查：SKILL.md 存在 → frontmatter 格式正确 → 无硬编码 token → 文件 ≤10KB。

### Step 4：生成 README

模板：标题 + description + 安装命令 `npx -y skills add KKKKhazix/<repo-name> -g --all` + 触发方式表 + 质量信息。

### Step 5：推送

```
cd <temp_dir>/<repo-name>
git init && git add -A && git commit -m "vX.X: initial release"
gh repo create KKKKhazix/<repo-name> --public --source=. --remote=origin --push
```

### Step 6：输出部署报告

| 项目 | 值 |
|------|-----|
| Skill | name vX.X |
| 仓库 | https://github.com/KKKKhazix/<repo-name> |
| 安装命令 | `npx -y skills add KKKKhazix/<repo-name> -g --all` |

## 约束

- 不修改源 SKILL.md
- 默认公开仓库（除非 `--private`）
- License 固定 MIT（除非指定其他）
- 部署后不删除临时目录

## 失败模式

| 失败 | 动作 |
|------|------|
| `gh auth status` 失败 | 输出"GitHub CLI 未认证，运行 `gh auth login`"→中止 |
| 仓库名冲突 | 输出冲突信息 → 给出三选项（覆盖/跳过/重命名）→ 等待选择 |
| `gh repo create` 失败 | 输出错误原因 → 中止 |
| SKILL.md 无版本块 | 降级：自动标注 v1.0 + 标记"版本块缺失请补充" |

## 完成标准
- 部署前检查: SKILL.md语法正确+ frontmatter完整+ 无循环依赖。
- 部署后验证: 触发词能正确路由+ 边界场景正确拒绝。
- 输出: 部署报告(路径+依赖关系+触发测试结果PASS/FAIL)。