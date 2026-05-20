# Changelog

All notable changes to **agents-md** are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.1.0] - 2026-05-20

### Added — Founding release

- Bilingual (English / 简体中文) standards for AI-native Git, GitHub, CI/CD,
  database and agent-behavior workflows.
- `STANDARDS.md` / `STANDARDS.zh-CN.md`: full single-file specification.
- `docs/`: chaptered, human-readable documentation in both languages.
- `skills/`: installable Claude Code Skill packages (superpower-style
  `SKILL.md` with YAML frontmatter) for:
  - `ai-native-git-workflow`
  - `ai-native-commits`
  - `ai-native-ci-cd`
  - `ai-native-database`
  - `ai-native-agent-behavior`
- `templates/`: drop-in `CLAUDE.md`, `AGENTS.md`, GitHub Actions workflow,
  and Claude Code `settings.json` template for downstream projects.
- Project entry points: `CLAUDE.md`, `AGENTS.md`, `docs/overview.md`,
  `docs/outlook.md`.
- Open-source meta: `LICENSE` (MIT), `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`.
