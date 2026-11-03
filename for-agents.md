# IL Accessibility Audit — instructions for AI agents

> תקציר בעברית: זהו סקיל לסוכני AI שבודק פרויקט אינטרנט לפי תקן ישראלי 5568 (חוק הנגישות). הורידו את הסקיל, התקינו אותו בתיקיית הסקילים של הסוכן (או הריצו את הסורק ישירות), ובקשו מהסוכן לבדוק את הפרויקט. ההוראות המלאות באנגלית למטה מיועדות לסוכן עצמו.

You are reading this because a user wants you to audit their web project for compliance with
Israeli accessibility law. This page tells you what the tool is, how to get it, and how to use it.

## What it is

`il-accessibility-audit` is an open-source skill in the [Agent Skills](https://agentskills.io)
format (a folder with `SKILL.md`, a Python scanner and reference docs). It audits HTML, JSX/TSX,
Vue, Svelte and CSS files against **Israeli Standard 5568**, the legal basis of the Israeli
accessibility-for-service regulations (2013). IS 5568 is WCAG 2.0/2.1 Level AA plus Israel-specific
requirements: `lang="he"` and `dir="rtl"` on Hebrew pages, and a mandatory accessibility statement
(הצהרת נגישות) linked from every page.

- Source: https://github.com/jordanhemo/il-accessibility-audit
- Skill instructions: https://github.com/jordanhemo/il-accessibility-audit/blob/main/il-accessibility-audit/SKILL.md
- Download (zip): https://github.com/jordanhemo/il-accessibility-audit/raw/main/dist/il-accessibility-audit.zip
- Download (.skill, for Claude.ai upload): https://github.com/jordanhemo/il-accessibility-audit/raw/main/dist/il-accessibility-audit.skill
- License: MIT. Requires Python 3 only.

## Install

Pick the one that matches your environment. The zip unpacks to a folder named
`il-accessibility-audit/`.

| Agent | Where the folder goes |
|---|---|
| Claude Code | `~/.claude/skills/il-accessibility-audit/` or `<project>/.claude/skills/` |
| Claude.ai / desktop | user uploads the `.skill` file in Settings → Skills |
| Codex CLI | `~/.codex/skills/il-accessibility-audit/` or `<project>/.agents/skills/` (older versions: `codex --enable skills`) |
| Cursor, Gemini CLI, Aider, other | anywhere in the project; read `SKILL.md` and follow it |

Shell version:

```bash
curl -L -o il-accessibility-audit.zip https://github.com/jordanhemo/il-accessibility-audit/raw/main/dist/il-accessibility-audit.zip
unzip il-accessibility-audit.zip -d ~/.claude/skills/     # or ~/.codex/skills/
```

## Run

From the skill folder, against the user's project:

```bash
python3 scripts/scan.py <project_dir> --format md --fix --report <project_dir>/accessibility-report.md
```

- Without `--fix` the scanner is read-only and prints findings (JSON by default, `--format md` for
  the Hebrew report).
- `--fix` edits only three things, because they have exactly one correct answer: adds
  `lang="he" dir="rtl"` to an `<html>` tag that has no `lang`, adds `dir="rtl"` to a Hebrew page
  missing it, and changes a positive `tabindex` to `tabindex="0"`.
- The report lists every finding with file, line, severity and the WCAG criterion or Israeli
  regulation it violates, then a section of findings that need a human decision, each with a
  concrete suggested fix.

## What not to guess

The skill deliberately leaves these to a human, and so should you:

- **Alt text.** Don't invent `alt="image"`. Ask the user what the image shows, or leave the
  suggestion in the report. An empty `alt=""` is correct only for purely decorative images.
- **Form labels.** The label text is content; propose wording, don't silently insert it.
- **Color contrast.** The scanner computes the real WCAG ratio. Changing brand colors is a design
  decision; report the ratio and the threshold (4.5:1 normal text, 3:1 large text).
- **The accessibility statement.** It is a legal disclosure that must contain the accessibility
  coordinator's real name and contact details and an update date. The skill ships a template
  (`references/accessibility-statement-template.md`); fill it only with details the user gives you.

## Limits to tell the user

The scan is static: it reads source files, not a rendered DOM, so it misses markup built at
runtime, and its contrast check cannot resolve CSS variables or inheritance. Say so in your
summary. The report is a working tool, not a certificate; a certified accessibility auditor is
still needed to attest compliance.

## Suggested first message to the user after installing

In Hebrew, since the users are Israeli site owners:

> התקנתי את הסקיל. אני יכול לסרוק את הפרויקט עכשיו, לתקן אוטומטית את מה שבטוח (lang, dir, tabindex) ולכתוב דוח בעברית עם כל הממצאים. על איזו תיקייה להריץ?
