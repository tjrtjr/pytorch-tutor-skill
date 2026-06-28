# PyTorch Tutor Skill Repository Design

## Goal

Publish the existing `pytorch-tutor` skill as a standalone public GitHub project that is easy to understand, inspect, and install.

## Repository

- Owner: `tjrtjr`
- Name: `pytorch-tutor-skill`
- Visibility: public
- Default branch: `main`
- License: MIT

## Contents

```text
pytorch-tutor-skill/
├── SKILL.md
├── README.md
├── LICENSE
└── reference/
    └── concepts.md
```

- `SKILL.md` and `reference/concepts.md` are copied exactly from the installed Codex skill.
- `README.md` explains the purpose, teaching behavior, directory structure, and installation commands in Chinese.
- `LICENSE` contains the standard MIT License.

## Publishing Flow

1. Create the standalone local Git repository.
2. Copy the existing skill files without changing their content.
3. Add the README and license.
4. Validate the skill frontmatter, referenced file paths, and source-file equality.
5. Commit on `main`.
6. Create `tjrtjr/pytorch-tutor-skill` as a public GitHub repository and push `main`.

## Success Criteria

- The public GitHub repository is accessible under the authenticated account.
- The remote `main` branch contains all four project files.
- The published `SKILL.md` and `reference/concepts.md` exactly match the installed skill.
- The README contains a working manual installation command for Codex and Claude Code.
- No credentials, project data, or unrelated files are committed.
