---
name: content-alchemist
description: "统一内容炼金管线。串联全流程：信源提取(weread-skills/exa-search)→结构化(dbs-content-system)→创作(khazix-writer/viral-writer)→去AI味(humanizer-zh)→多媒体(baoyu-slide-deck/baoyu-xhs-images)→分发(baoyu-post-to-*/crosspost)→验证(compile-and-verify)。触发词：内容炼金/全流程创作/从素材到发布/一篇文章从头到尾/内容管线/完整内容流程/帮我出篇文章带发布。不适用场景：单一环节任务→直接调用对应skill; 纯发布→baoyu-post-to-*; 纯写作→khazix-writer。正例：'帮我把这些读书笔记变成一篇公众号文章并准备好发布'→触发; '从我的微信读书划线里提炼3篇文章'→触发。反例：'帮我去掉AI味'→不触发→humanizer-zh; '帮我优化这个开头'→不触发→dbs-hook。"
version: "1.0.0 | created: 2026-06-08 | model: DeepSeek v4 Pro"
---

# Content-Alchemist — 统一内容炼金管线

把「从碎片素材到可发布媒体作品」的全流程压缩为一条可重复执行的技能管线。

## 核心哲学

大模型只做编排与质检。正文、标题、配图、分发由各子 skill 产出。content-alchemist 是管线的「总调度 + 门禁官」。

## 管线流程（7 阶段）

```
信源提取 ──▶ 内容结构化 ──▶ 创作 ──▶ 去AI味 ──▶ 多媒体 ──▶ 分发适配 ──▶ 终验
weread      dbs-content-   khazix-  humanizer  baoyu-*    baoyu-post  compile-and
exa-search  system         writer   -zh                   crosspost   -verify
```

---

## Phase 1: 信源提取

**目标**：从微信读书/Notion/Exa搜索中拉取 ≥3 条具体证据

**Skill 调用**：
- `weread-skills`：拉取划线/想法/书评（Token: `wrk-yC_PeQeCQBWIBD7_uFhTwwAA`）
- `exa-search`：扩展外部信源（Token: `c2549c02-e87f-40d3-a7d0-100bed139eb5`）
- 本地 Notion 导出：`D:\KnowledgeBase\notion\`

**输出**：`source_material.md`（每条素材带出处+原文摘录）

**门禁**：≥3 条有效素材；每条带可验证出处

---

## Phase 2: 内容结构化

**目标**：把素材拆解为 5 类内容单元

**Skill 调用**：`dbs-content-system`
- QST（问题）→ CON（概念）→ OPI（观点）→ CAS（案例）→ SOL（方案）

**输出**：`content_units.md`（每类 ≥1 个单元）

**门禁**：5 类单元齐全；单元间有 ≥2 条关系链接

---

## Phase 3: 创作

**目标**：根据目标平台生成正文

**Skill 路由**：
- 公众号长文 → `khazix-writer`（风格化深度长文）
- 多平台短内容 → `viral-writer`（公众号/小红书/抖音适配）
- 开头优化 → `dbs-hook`

**输出**：`draft.md`（正文 + ≥5 个标题备选 + 配图指导）

**门禁**：标题 ≥5 个；覆盖 ≥3 个内容驱动维度；字数在平台合理区间

---

## Phase 4: 去 AI 味

**目标**：消除 AI 痕迹，恢复活人感

**Skill 调用**：`humanizer-zh`
- 5 维诊断（句式/用词/结构/情感/细节）
- 禁用词扫描（赋能/抓手/闭环/底层逻辑/本质上/综上所述）
- 反例库对照

**输出**：`draft_humanized.md`

**门禁**：5 维均分 ≥3.5；零禁用词；含 ≥1 处具体细节；含 ≥1 处立场表达

---

## Phase 5: 多媒体

**目标**：生成适配平台的配图/封面/幻灯片

**Skill 路由**：
- 公众号封面 → `baoyu-cover-image`
- 小红书配图 → `baoyu-xhs-images`
- 幻灯片 → `baoyu-slide-deck`
- 信息图 → `baoyu-infographic`
- 漫画 → `baoyu-comic`

**输出**：`media_assets.md`（每种媒体的 prompt + 比例 + 位置建议）

**门禁**：封面图 prompt 具体（含风格/色调/构图）；正文配图标注插入位置

---

## Phase 6: 分发适配

**目标**：按平台规则生成发布版本

**Skill 路由**：
- 微信公众号 → `baoyu-post-to-wechat`
- 小红书 → `baoyu-post-to-wechat`（小红书格式）+ `baoyu-xhs-images`
- 推特/X → `baoyu-post-to-x`
- 飞书 → `feishu`
- 多平台 → `crosspost`

**输出**：`publish_ready/` 目录（每平台一个适配版本）

**门禁**：每平台适配版本齐全；格式符合平台规则

---

## Phase 7: 终验

**目标**：最终质量检查

**Skill 调用**：`compile-and-verify`
- 任务完成度检查
- 禁用词终扫
- 来源可追溯性检查

**输出**：`final_audit.md`（逐项核查）

**门禁**：全部核查项通过

---

## 产物目录结构

```
D:\KnowledgeBase\_alchemist\output\<日期>_<主题>\
├── source_material.md        # Phase 1 信源素材
├── content_units.md          # Phase 2 结构化单元
├── draft.md                  # Phase 3 初稿
├── draft_humanized.md        # Phase 4 去AI味稿
├── media_assets.md           # Phase 5 多媒体素材
├── publish_ready/            # Phase 6 分发版本
│   ├── wechat.md
│   ├── xiaohongshu.md
│   └── x.md
├── final_audit.md            # Phase 7 终验报告
└── TOOL_LEDGER.md            # 工具调用账本
```

---

## 大模型角色约束

**大模型只做**：
1. 阶段调度（决定进入哪个 Phase）
2. 门禁检查（每个 Phase 末逐条验证）
3. 异常处理（门禁不通过→回上一 Phase 重做）

**大模型禁止**：
- 亲自写正文 → 必须由 khazix-writer/viral-writer 产出
- 亲自去AI味 → 必须由 humanizer-zh 产出
- 跳过任何 Phase → 7 个 Phase 必须全部走完
- 跳过门禁 → 每个 Phase 末必须写入门禁检查结果

---

## 失败模式与兜底

| 失败模式 | 原因 | 兜底动作 |
|---------|------|---------|
| Phase 1 信源不足(<3条) | weread无相关划线或exa搜索无结果 | 扩大搜索词/放宽时间范围/提示用户补充素材 |
| Phase 2 结构化失败 | 素材主题过于分散 | 缩小选题范围，聚焦1个母题 |
| Phase 3 创作质量差(HKR<9) | 选题本身吸引力不足 | 回到Phase 1补充独特洞察或调整角度 |
| Phase 4 去AI味后质量反而下降 | 原文已有较强个人风格 | 仅做禁用词扫描，不改结构 |
| Phase 6 平台格式错乱 | 混淆了不同平台规则 | 逐条核对目标平台格式要求后重新适配 |
| 任意 Phase 连续 2 次门禁不通过 | 上游 Phase 输出有问题 | 回到出问题的 Phase 重做，不跳过 |

---

## G1-G6 自检

| 门禁 | 要求 | 状态 |
|------|------|------|
| G1 大小 | ≤10KB | ✅ |
| G2 触发层 | ≥5触发词/不适用场景/正反例/边界 | ✅ |
| G3 可执行 | 7 Phase 管线全部可执行 | ✅ |
| G4 验证 | 每 Phase 有布尔门禁 | ✅ |
| G5 失败兜底 | ≥6失败模式+兜底 | ✅ |
| G6 安全 | Token引用环境变量 | ✅ |