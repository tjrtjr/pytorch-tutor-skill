# Publish PyTorch Tutor Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish the installed `pytorch-tutor` skill as the public standalone GitHub repository `tjrtjr/pytorch-tutor-skill`.

**Architecture:** The repository root is itself an installable skill directory. The canonical installed `SKILL.md` and `reference/concepts.md` are copied byte-for-byte, while a Chinese README and MIT License provide project-facing documentation.

**Tech Stack:** Markdown, Git, GitHub CLI

---

### Task 1: Assemble the standalone skill repository

**Files:**
- Create: `SKILL.md`
- Create: `reference/concepts.md`
- Create: `README.md`
- Create: `LICENSE`

- [ ] **Step 1: Copy the canonical skill files**

Copy these files without changing their content:

```bash
cp /Users/junrongtang/.codex/skills/pytorch-tutor/SKILL.md SKILL.md
mkdir -p reference
cp /Users/junrongtang/.codex/skills/pytorch-tutor/reference/concepts.md reference/concepts.md
```

- [ ] **Step 2: Create the Chinese README**

Create `README.md` with:

```markdown
# PyTorch Tutor Skill

一个面向 PyTorch 初学者的 Codex / Claude Code Skill。它让 AI 在编写、修改或解释 PyTorch 代码时，不只给出能运行的代码，还会帮助学习者真正理解张量形状、关键设计和常见陷阱。

## 它会做什么

- 为关键张量操作标注形状，例如 `[B, T, D]`
- 用中文解释代码“做什么”以及“为什么这样写”
- 主动提醒梯度、设备、数据类型、训练/验证模式等常见问题
- 在代码后提供简短的学习卡片和动手验证建议

## 目录结构

```text
.
├── SKILL.md
└── reference/
    └── concepts.md
```

## 安装

### Codex

```bash
git clone https://github.com/tjrtjr/pytorch-tutor-skill.git \
  ~/.codex/skills/pytorch-tutor
```

### Claude Code

```bash
git clone https://github.com/tjrtjr/pytorch-tutor-skill.git \
  ~/.claude/skills/pytorch-tutor
```

安装后重启 Codex 或 Claude Code。

## 触发示例

- “教我看懂这段 PyTorch 训练循环”
- “解释这个张量为什么从 `[B, T, D]` 变成 `[B, D]`”
- “给这段模型代码加适合初学者的中文注释”

## License

MIT
```

- [ ] **Step 3: Add the MIT License**

Create `LICENSE` with the standard MIT License using:

```text
MIT License

Copyright (c) 2026 Junrong Tang

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### Task 2: Validate and commit the project

**Files:**
- Verify: `SKILL.md`
- Verify: `reference/concepts.md`
- Verify: `README.md`
- Verify: `LICENSE`

- [ ] **Step 1: Verify canonical source equality**

Run:

```bash
cmp /Users/junrongtang/.codex/skills/pytorch-tutor/SKILL.md SKILL.md
cmp /Users/junrongtang/.codex/skills/pytorch-tutor/reference/concepts.md reference/concepts.md
```

Expected: both commands exit successfully with no output.

- [ ] **Step 2: Verify structure and frontmatter**

Run:

```bash
test -f README.md
test -f LICENSE
test -f reference/concepts.md
head -1 SKILL.md | grep -Fx -- '---'
grep -Fx 'name: pytorch-tutor' SKILL.md
if rg -n '(github_pat_|ghp_|BEGIN [A-Z ]*PRIVATE KEY)' \
  SKILL.md README.md LICENSE reference docs; then exit 1; fi
git diff --check
```

Expected: all commands exit successfully, no credentials or private keys are found, and `git diff --check` reports no whitespace errors.

- [ ] **Step 3: Commit the repository files**

Run:

```bash
git add SKILL.md reference/concepts.md README.md LICENSE \
  docs/superpowers/plans/2026-06-28-publish-pytorch-tutor-skill.md
git commit -m "Publish PyTorch tutor skill"
```

Expected: Git creates a commit containing the skill, documentation, license, and implementation plan.

### Task 3: Create and publish the GitHub repository

**Files:**
- No local file changes

- [ ] **Step 1: Confirm GitHub identity and repository availability**

Run:

```bash
gh auth status
gh api user --jq .login
gh repo view tjrtjr/pytorch-tutor-skill
```

Expected: authentication succeeds as `tjrtjr`; the final command reports that the repository does not yet exist.

- [ ] **Step 2: Create the public repository and push `main`**

Run:

```bash
gh repo create tjrtjr/pytorch-tutor-skill \
  --public \
  --description "A beginner-friendly Chinese PyTorch tutoring skill for Codex and Claude Code" \
  --source . \
  --remote origin \
  --push
```

Expected: GitHub creates the public repository, configures `origin`, and pushes `main`.

- [ ] **Step 3: Verify the published repository**

Run:

```bash
gh repo view tjrtjr/pytorch-tutor-skill \
  --json nameWithOwner,visibility,defaultBranchRef,url
git status -sb
git remote -v
```

Expected: the repository is `PUBLIC`, the default branch is `main`, the local branch tracks `origin/main`, and the working tree is clean.
