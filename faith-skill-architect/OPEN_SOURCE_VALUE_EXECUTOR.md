---
name: open-source-value-executor
description: "Agent 自执行：每次被加载时，自动对 skill 仓库 + codewhale 环境执行价值提取。不是学习材料，是操作手册。触发词：value-executor、榨干价值、价值提取、自动化操作。不适用场景：用户明确要求暂停自动优化；纯任务执行（不触发自动审计）。正例：会话开始后自动触发；'跑一下价值提取流程'→触发。反例：'帮我写一篇文章'→不触发（用对应skill）。"
version: "1.0.0 | created: 2026-06-15 | target: skill-repo + codewhale"
---

# Open-Source Value Executor — Agent 自执行操作手册

> **不是给用户看的。是给我自己看的。**
> 每次被加载时，按此手册对当前 skill 仓库和 codewhale 环境执行价值提取。

---

## Agent 自检流程（每次会话首轮执行）

### Step 1：快速健康检查

```python
# 伪代码—每次加载后自动执行
1. 扫描 skills/ 下所有 SKILL.md 的 UTF-8 有效性
   - 发现损坏 → 立即触发修复协议（git checkout / bak 恢复）
2. 随机抽样 3 个 skill 检查 G1-G6 状态列
   - 发现 ? → 标记待修，加入本轮 TODO
3. 检查 skill-architect 是否最新
   - 如果不是当前会话创建的 → reload 并检查模式目录
```

**输出**：`checklist_write` 列出本轮待修复项

### Step 2：环境测绘（每轮做一次，结果缓存到会话）

```
测绘目标：
├── skill 仓库：89+ skill 的目录结构 + G1-G6 通过率
├── codewhale 原生 skills：~200+ skill 的家族分类
├── ECC (Codex) skills：~33 skill 的可用性检查
├── KnowledgeBase：content units / 素材 / 卡片
└── 工具链：Playwright / Python / gh CLI 可用性
```

**测绘方法**：用 `list_dir` + `grep_files` + `file_search` 组合，不跑 shell。

---

## 价值提取操作手册

按优先级从高到低排列。每次会话至少执行 P0-P1。

---

### P0：编码守护（每次写入必做）

**触发条件**：任何对 SKILL.md 的写操作

**执行清单**：
```
1. 写之前确认：
   - 文件不是 git repo？→ 检查 bak2 是否存在
   - 内容含 emoji（✅⚠🚫）？→ 工具写入，勿用 edit_file
   
2. 写之后立即验证：
   - read_file 工具能正常读取
   - 行数不异常（不应突然减少 50%）
   
3. 如果发现损坏：
   - STOP（不再编辑）
   - git checkout -- SKILL.md（有 git 时）
   - 或从 .bak2 恢复
   - 重建后用 read_file 验证
```

**这条不需要用户提，我每次写 skill 文件后自动做。**

---

### P1：G1-G6 门禁扫描（每次对话至少触发一次）

**触发条件**：
- 用户提到"质量"、"检查"、"评审"、"门禁"、"优化"
- 或我在对话中修改了任何 SKILL.md
- 或每 3 轮对话自动触发一次

**执行流**：
```
1. 选目标：
   - 我刚刚改过的 skill → 优先
   - 未通过 G1-G6 的历史清单中的下一个
   - 随机抽取 1 个

2. 对每个目标执行门禁检查：
   G1: 文件大小 ≤10KB（读文件长度）
   G2: description 含触发词(≥3) + 不适用 + 正反例
   G3: 有编号步骤（1. 2. 3. 正则匹配）
   G4: 有 checkbox 验证清单（- [ ] 模式）
   G5: 有失败模式表（| 失败模式 | 列）
   G6: 无硬编码凭据（token/password/api_key 模式匹配）

3. 输出到 `checklist_write`：
   - PASS 的标记 done
   - FAIL 的给出具体修复步骤
   - BLOCKED（G1/G2/G6 失败）的优先排前
```

**操作示例**：
```
我改完 diff-reviewer 后 → 自动跑 G1-G6 门禁 → 发现 G2 触发条件是占位符 → 修复 → 再验证 → PASS
```

---

### P2：跨 skill 模式挖掘（每周历或每 10 次 skill 操作触发一次）

**触发条件**：已累计处理 ≥5 个 skill 文件后

**执行流**：
```
1. 收集本轮改过的 skill 中发现的共同模式：
   - 写了类似结构 → 提炼为模式 P9+
   - 犯了类似错误 → 提炼为反模式 A9+
   
2. 追加到 skill-architect 的 P 系列 / A 系列目录：
   - 打开 skill-architect/SKILL.md
   - 找到 ## ▎设计模式目录
   - 追加新条目（格式参照 P1-P8）
   - 追加后验证 G1（≤10KB 限制）

3. 更新 r3-playbook 的统计（如果改动了模式数量）：
   - 记录这次是第几个模式
   - 来源标注为本次操作的 skill
```

**操作示例**：
```
修复 weread-exporter 时发现"备份文件.bak2"的恢复模式
→ 提炼为 P9：多级备份恢复模式
→ 追加到 skill-architect
```

---

### P3：反模式识别与入库（发现即做）

**触发条件**：我在修复/评审 skill 时发现重复性错误

**执行流**：
```
1. 识别新反模式时：
   - 检查 skill-architect 的 A 系列是否已有
   - 如果没有 → 追加（格式：| 名称 | 表现 | 根因 | 修复 |）
   - 如果有但表述不全 → 更新

2. 每个反模式附带一个真实案例引用：
   - "A5 GBK写emoji — 例：weread-exporter 和 40 个 skill 集体损坏"

3. 如果同一个反模式出现 ≥3 次 → 升级为"系统性风险"：
   - 在 OPEN_SOURCE_VALUE_STRATEGY.md 的 P0 编码守护中加对应规则
   - 确保不会再犯
```

---

### P4：生态测绘与整合（每周历首次会话执行）

**触发条件**：每周第一次对话，或用户提到"家族"、"体系"、"整合"

**执行流**：
```
1. 测绘 skill 仓库的家族分布：
   - dbs-* 家族: 22 个 → 检查 dbs-orchestrator 路由表是否完整
   - content-* 家族: 6 个 → 检查 content-alchemist 管线是否完整
   - skill-* 家族: 10 个 → 检查相互引用是否正确
   - 独立 skill: 其余 → 分类到功能组

2. 测绘 codewhale 原生技能（~/.codewhale/skills/）：
   - 哪些和现有 skill 重复？→ 标记去重候选
   - 哪些是现有 skill 的上游/下游？→ 更新路由表

3. 测绘 ECC skills（~/.codex/.agents/skills/）：
   - 对比已入库的 ECC skill 版本是否一致
   - 发现新 skill → 评估是否需要导入

4. 输出：
   - 家族健康报告（有哪些缺口、重复、不一致）
   - 推荐整合行动（合并/淘汰/新增）
```

---

### P5：即时代码级优化（发现即做）

**触发条件**：读取任何 SKILL.md 时发现可优化点

**执行清单**：
```
□ 触发条件是占位符 → 立即用 frontmatter 的描述替换
    例: "涉及本 skill 的工作任务" → 从 description 提取触发词

□ G1-G6 状态列是 ? → 立即评估填充（✅/❌）

□ 失败模式表只有表头 → 基于领域知识填充 3 行

□ 无版本脚注 → 追加（用当前日期）
    "*version: 1.0.0 | optimized: YYYY-MM-DD | methodology: auto | model: DeepSeek v4 Pro*"

□ 重复段落（两个验证清单）→ 删除重复的

□ 硬编码路径 → 替换为模板变量
    "C:\Users\董辉" → "{USER_HOME}" 或 "C:\Users\%USERNAME%"

□ 无 DS v4 Pro 适配 → 追加标准适配表（从任何现有 skill 复制）

□ 无 Token 预算 → 追加标准预算表
```

**这条最实用**——每次我打开一个 SKILL.md 都应该自动过这个清单。

---

## codewhale 环境专项

### CW1：技能库索引

每次测绘时对比 `~/.codewhale/skills/` 和 `C:\Users\董辉\.agents\skills\`：

```
重复检测方法：grep_files 搜索 description 或 name 字段
- 完全重复 → 标记"二选一"到 checklist
- 部分重叠 → 标记"合并候选"
- 互补 → 标记"联动候选"，更新路由
```

### CW2：工具链可用性

每次测绘时更新可用工具清单：

```
工具检测方法：exec_shell 轻量检查
- python --version
- node --version
- gh --version
- playwright --version（或检查是否安装）
- git --version
```

可用工具影响到我执行 skill 的方式——有的 skill 依赖 python，有的依赖 node。

### CW3：已入库 skill 的质量基线

每次测绘后更新一份基线数据：

```
基线格式：
dbs-orchestrator: G1=10KB|G2=含触发词|G3=可执行|G4=有|G5=有|G6=有
prompt-compiler:  G1=✅|G2=✅|G3=✅|G4=✅|G5=✅|G6=✅  
```

未达基线的就是下一轮 P1 目标。

---

## 执行保障

### 例外规则
- 如果用户显式说"不要自动优化" → 暂停所有 P0-P5 自动触发，只响应明确指令
- 如果当前对话在密集执行复杂任务 → 推迟测绘类操作到对话自然中断时

### 成功指标
```
每个会话结束时自动检查：
□ 是否至少执行了 1 次 P0（编码守护）
□ 是否至少检查了 1 个 skill 的 G1-G6（P1）
□ 如果改了 skill → 是否更新了反模式/P 系列（P2/P3）
□ 如果是本周首次 → 是否做了生态测绘（P4）
□ 每次读 SKILL.md → 是否自动过了 P5 优化清单
```

### 优先级速查

```
遇到冲突时：
  用户明确指令 > P0(编码) > P5(即时代码优化) > P1(门禁) > P3(反模式) > P2(模式) > P4(测绘)
  安全 > 质量 > 结构 > 知识 > 生态
```

---

## 一句话工作原则

> **每次打开一个 SKILL.md，必须让它比我打开前更好。哪怕只是加了一个版本脚注。**
