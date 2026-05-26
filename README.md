# PresentationSkill-ClaudeCode

Three Claude Code skills for producing **print-quality, defensible, single-source-of-truth** publishing artifacts — posters, decks, and research synthesis — through a unified HTML + Playwright pipeline.

Designed for forensic-analytical work (investigation, audit, postmortem, RFC, market analysis, research summary). Opinionated about typography, citation discipline, overflow protection, and semantic color use. Self-contained HTML output that survives projection, prints cleanly to vector PDF, and never looks AI-generated.

---

## Skills

### 📊 `report-synthesis` — narrative editor
Pile of source material (docs, transcripts, notes, datasets) → typed, citation-rigorous outline (`outline.md` + `evidence.md`) that downstream render skills consume. **Editorial only**, no visual rendering.

### 🎞️ `dark-deck-report` — render: presentation
Outline → dense, self-contained HTML deck. McKinsey conclusion-first structure, dark forensic aesthetic (with matching light inversion). Three depths: 14-slide full / 4-slide brief / 1-slide memo. Fixed 16:9 canvas where overflow is a build error.

### 🖼️ `poster-maker` — render: single-page poster
Markdown / notes → publication-quality A0/A1/A2 poster (portrait or landscape). Infographic and minimal modes. Smart template routing via density score, semantic color system, geometric SVG illustration decision engine. Open in Chrome → print → vector PDF.

**Designed pipeline:** `report-synthesis` → (`dark-deck-report` OR `poster-maker`).
Skills are independent — each can also be invoked standalone.

---

## Installation

Clone into your Claude Code skills directory:

```bash
# Windows
git clone https://github.com/ZeroAlcoholic/PresentationSkill-ClaudeCode.git "%USERPROFILE%/.claude/skills"

# macOS / Linux
git clone https://github.com/ZeroAlcoholic/PresentationSkill-ClaudeCode.git ~/.claude/skills
```

Or merge into an existing `~/.claude/skills/`:

```bash
cd ~/.claude/skills
git clone https://github.com/ZeroAlcoholic/PresentationSkill-ClaudeCode.git tmp && \
  mv tmp/* tmp/.[!.]* . && rmdir tmp
```

Restart Claude Code. The skills become invocable by name (`poster-maker`, `dark-deck-report`, `report-synthesis`).

---

## Requirements

- Claude Code CLI
- Playwright MCP plugin (for rendering posters and decks to PNG)
- Python ≥ 3.10 (only `report-synthesis/scripts/` and local HTTP server during render)
- Chrome / Chromium (for `Ctrl+P → Save as PDF` vector export)

Fonts (auto-loaded via Google Fonts CDN): IBM Plex Sans, IBM Plex Mono, Noto Sans TC.

---

## Repository Layout

```
.claude/skills/
├── poster-maker/
│   ├── SKILL.md
│   ├── templates/          # poster-infographic-{portrait,landscape}, poster-minimal
│   └── references/         # layout-patterns, typography-scale, visual-storytelling, illustration-patterns, export-guide
├── dark-deck-report/
│   ├── SKILL.md
│   ├── templates/
│   └── references/
└── report-synthesis/
    ├── SKILL.md
    ├── templates/
    ├── references/
    └── scripts/
```

---

## License

Personal use. No warranty. Skill behavior, templates, and reference rules may change between commits without notice.
