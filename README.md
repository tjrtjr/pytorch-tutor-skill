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
