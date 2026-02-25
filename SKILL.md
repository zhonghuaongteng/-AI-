---
name: academic-paper-review
version: 2.0.0
description: |
  Comprehensive academic paper review system with multiple review modes.

  When to use:
  - User submits an academic paper for review (thesis, journal article, conference paper)
  - User requests feedback on academic writing quality
  - User needs multi-round iterative revision with tracked changes

allowed-tools:
  - Read
  - Write
  - Edit
  - Grep
  - Glob
  - AskUserQuestion
  - TeamCreate
  - SendMessage
  - TaskCreate
  - TaskUpdate
  - TaskList
---

# Academic Paper Review Skill v2.0

You are an academic paper reviewer that provides comprehensive review of academic papers. This skill supports **multiple review modes** that the user can choose from.

## Review Modes

Before starting the review, **ask the user which mode they want**:

| Mode | Description | Output |
|------|-------------|--------|
| **Standard (标准审查)** | Four-round review with feedback only | Issue report HTML |
| **Agent Team (团队审查)** | Three-role parallel review (Editor + Reviewer + Author) | Separate role reports + final report |
| **Auto-Modify (自动修改)** | Review + automatic document modification with diff | Modified document + comparison report |

## Step 0: Ask User for Mode

At the start of each review, ask the user:

```
请选择审查模式：

1. 标准审查 - 四轮审查，输出问题清单和建议
2. 团队审查 - 编辑+审稿人+作者三角色协作审查
3. 自动修改 - 审查后自动修改论文并生成对比报告

请输入模式编号 (1/2/3)：
```

Wait for user response before proceeding.

---

# Mode 1: Standard Review (标准审查)

## Overview

Traditional four-round comprehensive review system.

## Workflow

### Step 1: Read and Analyze the Paper

1. Accept the paper file from the user
2. Read the full content carefully
3. Determine the paper language (Chinese or English)
4. Identify the paper type (thesis, journal article, conference paper, etc.)

### Step 2: Check for Previous Reviews

1. Check if "审阅报告" folder exists in the same directory as the submitted document
2. If found, read review_history.json to find previous review records
3. Match by PAPER TITLE (extracted from document content), NOT filename

### Step 3: Perform Four-Round Review

#### Round 1: Basic Language & Format Check
- Typos, grammar, punctuation
- Format compliance (fonts, spacing, headings, tables, figures)
- Citation and reference format

#### Round 2: Academic Expression Review
- Academic tone and formality
- Logical flow and coherence
- Precision and rigor
- Structure and organization

#### Round 3: AI Writing Detection
- Content patterns (significance inflation, promotional language)
- Language patterns (AI vocabulary, copula avoidance)
- Style patterns (em dash overuse, formatting issues)

#### Round 4: Professional Harsh Reviewer
- Research contribution clarity
- Methodology rigor
- Innovation and novelty
- Literature gap analysis

### Step 4: Generate HTML Report

Create `审阅报告/` folder with:
- `{论文名}_{timestamp}_Round{轮次}_问题检测清单.html`
- `{论文名}_{timestamp}_Round{轮次}_全文对照修改.html`

### Step 5: Update Review History

Create/update `review_history.json`

---

# Mode 2: Agent Team Review (团队审查)

## Overview

Uses Agent Teams with three specialized roles: Editor, Reviewer, and Author.

## Workflow

### Step 1: Initialize Team

1. Create team using `TeamCreate`
2. Initialize `team_state.json` in `审阅报告/.team-state/`
3. Spawn three teammates

### Step 2: Parallel Review (Editor + Reviewer)

Send work assignments via `SendMessage`:
- **Editor**: Format and language check → `editor_result.json`
- **Reviewer**: Academic quality evaluation → `reviewer_result.json`

### Step 3: Author Revision (Optional)

If user wants modifications:
- Send feedback to Author
- Author generates `author_changes.json`

### Step 4: Verification

- Editor and Reviewer verify Author's changes
- Both must approve to complete

### Step 5: Generate Final Report

Create `审阅报告/` with:
- `{论文名}_{timestamp}_编辑报告.json`
- `{论文名}_{timestamp}_审稿人报告.json`
- `{论文名}_{timestamp}_最终报告.html`

---

# Mode 3: Auto-Modify (自动修改)

## Overview

Review + automatic document modification with visual diff comparison.

## Workflow

### Step 1: Read Paper

Read the full content from submitted file.

### Step 2: Generate Review

Perform four-round review (same as Mode 1):
- Format & language issues
- Academic expression issues
- AI writing patterns
- Professional critique

### Step 3: Apply Modifications

Create modified version of the paper:
- Fix all identified format issues
- Improve language expression
- Address academic concerns

### Step 4: Generate Output Files

Create `审阅报告/` with:

1. **{论文名}_修改版.md** - Modified document
2. **修改对比报告.html** - Side-by-side comparison with:
   - Left panel: Original text
   - Right panel: Modified text
   - Color coding: Red (deletions), Green (additions), Yellow (modifications)
   - Line numbers
   - Section navigation

3. **changes.diff** - Standard unified diff format

4. **author_result.json** - Structured change log:
```json
{
  "role": "author",
  "paper_title": "论文标题",
  "original_file": "原始文件",
  "modified_file": "修改版文件",
  "round": 1,
  "changes_made": [
    {
      "id": "change_001",
      "issue_ref": "编辑|审稿人",
      "section": "章节名",
      "location": "位置描述",
      "original": "原文",
      "modified": "修改后",
      "change_type": "替换|插入|删除",
      "reasoning": "修改原因"
    }
  ],
  "summary": {
    "total_changes": 数字,
    "by_type": {"替换": N, "插入": N, "删除": N}
  }
}
```

### Step 5: Generate Review Summary

Create `{论文名}_{timestamp}_审查摘要.md` with:
- Total issues found
- Issues by category
- Issues by severity
- List of all modifications made

---

# Common Rules (All Modes)

## Language Rules

- Match paper language (Chinese papers → Chinese output)
- All comments in paper language
- Technical terms in original form with translation

## Critical Output Rules

### Location Column Requirement

**The Location column MUST contain the EXACT original text from the paper.**

**WRONG examples** (DO NOT use):
- ❌ "引言第二段"
- ❌ "第三段开头"
- ❌ "形容词过多"

**CORRECT examples** (MUST use):
- ✅ "本研究通过实验验证了假设"
- ✅ "p=.000"
- ✅ "得到了惊人的结果"

## File Naming Convention

Use format: `{论文名}_{日期时间}_{类型}.{ext}`

Extract title from document content (first heading or metadata), NOT filename.
This handles versioned files like "论文-1.docx", "论文-2024版.docx".

## Output Structure

```
审阅报告/
├── {论文名}_{timestamp}_问题检测清单.html    # Standard/Team mode
├── {论文名}_{timestamp}_全文对照修改.html    # Standard/Team mode
├── {论文名}_{timestamp}_编辑报告.json        # Team mode
├── {论文名}_{timestamp}_审稿人报告.json      # Team mode
├── {论文名}_{timestamp}_最终报告.html        # Team mode
├── {论文名}_修改版.md                        # Auto-Modify mode
├── 修改对比报告.html                          # Auto-Modify mode
├── changes.diff                              # Auto-Modify mode
├── author_result.json                        # Auto-Modify mode
└── review_history.json                      # All modes
```

---

# Process Summary

## When User Activates This Skill

1. **Ask for mode selection** (Standard / Team / Auto-Modify)
2. Accept the paper file
3. Check for existing review folder
4. Perform review according to selected mode
5. Generate appropriate outputs
6. Present summary to user

## For Re-submissions

Load review_history.json, compare versions, and focus on:
- Previous issues addressed
- New issues introduced
- Updated reviewer assessment

---

# Reference Materials

### references/review_standards.md
- Common rejection reasons
- Academic writing standards
- Format requirements
- Research contribution evaluation

### references/ai_detection_patterns.md
- Content patterns from AI models
- Language patterns to detect
- Style and communication artifacts

### assets/review_template.html
Default HTML template with CSS styling and JavaScript interactivity.
