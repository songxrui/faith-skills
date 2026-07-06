---
name: content-pipeline-auditor
description: |
  内容产线全链路审计。监控 DBS 流水线产出率、CJK 达标率、卡片覆盖率、发布频率、跨平台同步状态，输出量化仪表盘。
  
  适用场景:
  - 想知道"过去一周产了多少篇，多少达标"
  - 内容产线出了瓶颈，要定位哪个环节卡住了
  - 月度/周度内容产出回顾
  - 对比两个时段的内容产出效率
  
  不适用场景:
  - 单篇文章质量诊断 → dbs-content
  - Skill 质量评测 → skill-review
  - 知识库结构检查 → knowledge-base-health
  - 具体的内容创作/改写 → dbs-content-system / khazix-writer
  
  触发词: 内容产线, pipeline审计, 产出统计, CJK达标率, 内容复盘,
           创作效率, 内容仪表盘, 产线健康, 发表统计, 覆盖率检查
  
  边界: content-pipeline-auditor 审计产出数量+效率+达标率，不审计单篇质量。
        dbs-content 诊断单篇质量，knowledge-base-health 检查KB结构。
  
  正例: "统计这周写了多少篇文章"→触发 / "内容产线最近效率怎么样"→触发 / "检查CJK达标率"→触发
  反例: "这篇文章质量如何"→dbs-content / "知识库有没有重复文件"→knowledge-base-health
---

# Content Pipeline Auditor v1.0 — 内容产线全链路审计

## 环境
- 内容目录: `D:\KnowledgeBase\media\wechat_2026-06-07_dbs\` (主产出)
- 卡片目录: `D:\KnowledgeBase\cards\` (35张深度卡)
- 报告输出: `D:\KnowledgeBase\_logs\reports\pipeline-YYYYMMDD-HHmm.md`
- 命令: PowerShell; CJK检测: Unicode范围判断

## 审计维度(7维)

| # | 维度 | 指标 | 计算方式 |
|---|------|------|---------|
| M1 | 产出总量 | 总文章数 | `(Get-ChildItem D*.md).Count` |
| M2 | CJK达标率 | ≥3000 CJK 的比例 | 逐篇统计CJK字符(Unicode>127)，达标篇/总篇×100% |
| M3 | 卡片覆盖率 | 已覆盖卡片/总卡片 | 扫描文章引用卡片ID(如C1-1)，去重计数/35 |
| M4 | 发布频率 | 日均产出 | 按文件修改时间分组到日，计算日均 |
| M5 | 字数分布 | min/max/median CJK | 全量统计，输出五数概括(min/Q1/median/Q3/max) |
| M6 | 趋势对比 | vs 上次审计 | 读取上次报告 → 对比 M1-M5 → 标记 ↑/↓/→ |
| M7 | 瓶颈定位 | 未覆盖卡片+低CJK文章 | 标记 CJK<2500 的文章为"需补足"，未覆盖卡片为"待产出" |

## 审计流程

### Step 1: 环境验证
   - `Test-Path "D:\KnowledgeBase\media\wechat_2026-06-07_dbs\"` → 存在则继续
   - 统计: D*.md 文件数 N

### Step 2: CJK 全量扫描(最耗时，约3-5秒/100篇)
   ```powershell
   $files = Get-ChildItem "D:\KnowledgeBase\media\wechat_2026-06-07_dbs\D*.md"
   $results = @()
   foreach ($f in $files) {
       $c = Get-Content $f.FullName -Raw -Encoding UTF8
       $cn = ($c.ToCharArray() | Where-Object { [int]$_ -gt 127 }).Count
       $results += [PSCustomObject]@{File=$f.Name; CJK=$cn; Pass=($cn -ge 3000)}
   }
   ```
   - 输出: 达标数/总数，最低/最高/中位 CJK，未达标列表

### Step 3: 卡片覆盖率检测
   ```powershell
   $cards = (Get-ChildItem "D:\KnowledgeBase\cards\C*.md").BaseName
   $covered = @()
   foreach ($card in $cards) {
       $pattern = $card -replace '^C\d-\d_',''
       $match = Select-String -Path "D:\KnowledgeBase\media\wechat_2026-06-07_dbs\D*.md" -Pattern $pattern -List
       if ($match) { $covered += $card }
   }
   ```
   - 输出: 覆盖率 = $covered.Count / 35 × 100%；未覆盖列表

### Step 4: 发布频率统计
   - 按文件修改时间的日期分组: `Get-ChildItem | Group-Object { $_.LastWriteTime.ToString('yyyy-MM-dd') }`
   - 输出: 日均产出、最高单日产、最长连续发布天数、最长断更天数

### Step 5: 瓶颈诊断
   - 低 CJK 文章(<2500): 列出文件名+CJK → 标记"需补足"
   - 高 CJK 低覆盖: 文章多但卡片覆盖率<50% → 标记"选题集中度过高"
   - 断更>3天: 标记"产线中断风险"
   - 未覆盖卡片: 按簇分组列出 → 标记"待产出"

### Step 6: 趋势对比(如上次报告存在)
   - 读取 `<报告目录>/` 中最近一次报告
   - M1-M6 逐项对比，输出 ↑/↓/→ 和变化幅度

### Step 6.5: 审计验证
   - CJK达标验证: `$failCount -eq 0` → 通过，`>0` → 存在未达标文章
   - 覆盖率验证: `$coveredCount -ge 35` → 100%覆盖通过，`<35` → 存在未覆盖卡片
   - 断更验证: `$maxGap -le 2` → 通过(断更≤2天)，`>2` → 触发告警

### Step 7: 输出仪表盘

```markdown
# 内容产线审计报告
> 扫描: [YYYY-MM-DD HH:MM] | 目录: wechat_2026-06-07_dbs | 耗时: Xs

## 仪表盘
| 指标 | 当前值 | 上次 | 趋势 | 目标 | 状态 |
|------|--------|------|------|------|------|
| 总文章数 | N | - | - | - | - |
| CJK达标率 | XX% (X/N) | - | - | 100% | ✅/❌ |
| 卡片覆盖率 | XX% (X/35) | - | - | 100% | ✅/⚠️ |
| 日均产出 | X.X篇/天 | - | - | ≥1 | ✅/⚠️ |
| 中位CJK | XXXX | - | - | ≥3200 | ✅/⚠️ |
| 最长断更 | X天 | - | - | ≤2天 | ✅/❌ |

## CJK 五数概括
| Min | Q1 | Median | Q3 | Max |
|-----|----|--------|----|-----|

## 未达标题(需补足)
| 文件 | CJK | 缺口 |
|------|-----|------|

## 未覆盖卡片(待产出)
| 卡片 | 所属簇 | 主题 |
|------|--------|------|

## 瓶颈诊断
| 瓶颈类型 | 详情 | 建议 |
|---------|------|------|

## 改进优先级
| 优先级 | 动作 | 预期效果 |
|--------|------|---------|
```

## 失败兜底
| 失败 | 动作 |
|------|------|
| 内容目录不存在 | 输出 "目录不可达" → 中止 |
| 无 D*.md 文件 | 输出 "该目录无文章" → 中止 |
| cards/ 目录不可达 | M3 标记 N/A → 继续其他维度 |
| 上次报告不存在 | M6 标注"首次审计" → 继续 |

## 约束
- 只读不写: 不修改任何文章或卡片
- 报告保存: `D:\KnowledgeBase\_logs\reports\pipeline-YYYYMMDD-HHmm.md`
- 建议频率: 每周1次 或 每产出20篇后

---
## 版本
- v1.1 | 2026-06-07 | R2: 周报自动生成+告警阈值
- v1.0 | 2026-06-07 | 初始版本(收费产品级) | 原因: 内容产线缺少量化仪表盘，决策靠"感觉"非数据
## R2增强: 周报自动生成 + 告警阈值

### 周报模式
触发: "生成内容周报" / "weekly content report"
自动生成 `D:\KnowledgeBase\_logs\reports\weekly-YYYY-MM-DD.md`:
```markdown
# 内容周报 [YYYY-MM-DD ~ YYYY-MM-DD]
| 指标 | 本周 | 上周 | 趋势 |
|------|------|------|------|
| 新增文章 | N | N | ↑/↓ |
| CJK达标率 | XX% | XX% | ↑/↓ |
| 累计文章 | N | N | ↑/↓ |
| 卡片覆盖 | X/35 | X/35 | ↑/↓ |
```

### 告警阈值(任一触发=标红)
| 指标 | 黄警 | 红警 |
|------|------|------|
| CJK达标率 | <95% | <90% |
| 断更天数 | >3天 | >7天 |
| 卡片覆盖率 | <80% | <60% |
| 日均产出 | <0.5 | <0.2 |

告警输出: `⚠️ [指标] [当前值] 触发[黄/红]警 → [建议动作]`