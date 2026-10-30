---
name: "speckit-workflow"
description: "Use when the user wants to build a project or feature with Spec-Kit / spec-driven development (speckit, /speckit.*, specify init, spec → plan → tasks → implement), or needs help installing or running GitHub Spec-Kit."
---

# Spec-Kit Workflow (Spec-Driven Development)

Use Spec-Kit to build features in a structured way instead of one giant prompt:
constitution → specify → clarify → plan → checklist → tasks → analyze → implement.

Reply in the user's language; keep commands/code in English.

## 0. Before starting

1. Confirm the working folder (project root). Ask if unclear.
2. Confirm whether it is a **new** project or an **existing** one.
   - Existing project → Spec-Kit builds the spec on top of the existing code; read the codebase and any `CLAUDE.md` / README first.
   - New project → the user writes the feature description; help them shape it.
3. Find out the stack (from `CLAUDE.md`, `package.json`, `composer.json`, etc., or ask). The plan must name it explicitly.
4. Check the tooling: `specify --version` (and `uv --version`). If missing, install (step 1).

## 1. Install (only if missing)

```bash
# uv — macOS / Linux (then open a new terminal)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# uv — Windows (PowerShell, then open a new terminal)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

```bash
# specify-cli — latest
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
# upgrade later
specify self upgrade
```

- `uv` downloads a Python for itself if none is installed; Git must be installed (the CLI is installed from a git URL).
- If `specify` is "not found" after installing, run `uv tool update-shell` and open a new terminal.

## 2. Init the project

```bash
# new project
mkdir <project> && cd <project>
specify init --here --integration claude --force

# existing folder
specify init . --integration claude
```

- Correct flag is `--integration claude`, and `--force` with two normal hyphens (not an em dash `—force`).
- Init adds `.specify/` (templates, scripts, memory/constitution) and the commands under `.claude/`.
- Commands appear as `/speckit.specify` or `/speckit-specify` depending on version — same command; use whichever exists.
- After init, the user may need to restart Claude Code for the new commands to show up.

## 3. The workflow (in order)

| # | Command | What it does | Gate |
|---|---------|--------------|------|
| 1 | `/speckit.constitution` | Project principles (constitution.md) | User provides/approves rules |
| 2 | `/speckit.specify` | Feature description (AR/EN) → `specs/NNN-name/spec.md` | User reviews spec |
| 3 | `/speckit.clarify` | Optional but recommended: asks questions about ambiguities | User answers |
| 4 | `/speckit.plan` | `plan.md` architecture/design — state the stack explicitly | User reviews plan |
| 5 | `/speckit.checklist` | Quality gate for requirements (design / UX / RTL / accessibility) | Fix gaps |
| 6 | `/speckit.tasks` | Split plan into executable tasks | — |
| 7 | `/speckit.analyze` | Optional: check spec / plan / tasks are consistent | Resolve issues |
| 8 | `/speckit.implement` | Execute tasks and write code | — |

Rules:
- Stop at each gate and show the user a short summary of the generated file before moving on. Never jump to implement without an approved spec + plan.
- Short path when the user wants speed: constitution → specify → plan → tasks → implement.
- New project with authentication: make login work end to end before any other feature.
- Model hint (Claude Code): a stronger model (Opus) for constitution/specify/clarify/plan; a faster one (Sonnet) is fine for implement.

## 4. Constitution vs CLAUDE.md

- `.specify/memory/constitution.md` = the principles for THIS project (code style, testing rules, security rules, stack choices). Written by `/speckit.constitution`.
- `CLAUDE.md` (if the project has one) = project facts and conventions Claude reads every session. Don't duplicate it into the constitution — reference it instead.
- The plan's Constitution Check covers both.

## 5. `/speckit.plan` details

It takes `spec.md` and produces the technical plan:
- Reads `constitution.md` + `spec.md`.
- Fills **Technical Context** with the stack and versions (e.g. PHP 8.3, Laravel 12, MySQL, Blade + Bootstrap; or Vue 3 + Vite + Pinia + Tailwind + Axios).
- Runs the **Constitution Check gate** — the plan must comply with the constitution.
- **Phase 0 → `research.md`**: resolves ambiguous technical decisions (local vs S3 image storage, how net profit is calculated…).
- **Phase 1 → `data-model.md`** (entities & relations) + `contracts/` (API endpoints, or web routes + forms for server-rendered apps) + `quickstart.md` (run/test steps).

Run `/speckit.plan` plain first, review `research.md` and `data-model.md`, then steer if needed, e.g.:
```
/speckit.plan Use spatie/laravel-medialibrary for images, and use Route Model Binding in all admin routes.
```

## 6. Organizing features

Each feature gets its own `/speckit.specify` and a numbered folder:
```
specs/
├── 001-authentication/spec.md
├── 002-products/spec.md
└── 003-cart/spec.md
```
Finish one feature → start the next with a new `/speckit.specify`. A "feature" = a complete capability (login, products, cart) — not every small edit. Not too small, not too big.

## 7. Figma designs

Before `/speckit.implement` on a Figma design: make sure Figma MCP is connected and authenticated, and get the specific `node-id`. Read the design first, then run the full chain:
```
using figma mcp can you read this figma pages <figma-url>?node-id=...
using speckit make all of this to implement the figma design into pages and make it in small tasks with best practice
/speckit-specify → /speckit-clarify → /speckit-plan → /speckit-checklist → /speckit-tasks → /speckit-analyze → /speckit-implement
```
Any task converting a whole Figma design or touching multiple features should go through Plan Mode / the full chain.

## 8. Arabic-first / RTL projects

If the project is Arabic-first, put RTL in the spec and always run `/speckit.checklist` with a prompt like:
> تحقق من جودة التصميم و UX للصفحات العامة ولوحة الأدمن معًا: هوية بصرية موحدة (ألوان، خطوط، تباعد)، تباين لوني كافٍ (accessibility)، تصميم متجاوب (Responsive) لكل صفحة، دعم RTL واضح لكل عنصر تفاعلي، حالات التحميل/الأخطاء/النجاح موصوفة وقابلة للاختبار.

## 9. Example `/speckit.specify` prompt (medical device maintenance center)

```
مشروع لمركز صيانة أجهزة طبية، بواجهة Blade + Bootstrap. المطلوب:
1. صفحة عامة (Public) تعرض:
   - الأجهزة المعروضة للبيع بصورها وأسعارها ووصفها.
   - الأجهزة اللي تم صيانتها: صورة قبل وصورة بعد ووصف العطل.
   - صفحة خدمات المركز.
2. لوحة تحكم للأدمن بعد تسجيل الدخول:
   - إضافة/تعديل/حذف جهاز للبيع (اسم، وصف، سعر، صور، حالة التوفر).
   - إضافة/تعديل/حذف سجل صيانة: صورة قبل، صورة بعد، وصف العطل، تاريخ دخول، تاريخ خروج.
   - تسجيل تكلفة الصيانة وقطع الغيار والمبلغ المدفوع، مع حساب صافي الربح تلقائيًا.
   - قائمة بكل الأجهزة تحت الصيانة مع التواريخ والحالة (تحت الصيانة / تم التسليم) وإجمالي الأرباح.
المشروع Arabic-first، لازم دعم RTL في التصميم.
```

## 10. When `/speckit.*` commands are not available

Run `specify init` in the project folder, then follow the same stages manually:
- Read the templates in `.specify/templates/` (spec, plan, tasks, checklist) and any helper scripts in `.specify/scripts/`.
- Write `.specify/memory/constitution.md`, then `specs/NNN-feature/spec.md`, `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md`, `checklists/`, `tasks.md` exactly per the templates.
- Keep the same gates: stop for user review after spec, clarify, plan, and checklist; implement tasks in order and tick them off in `tasks.md`.

Reference: https://github.com/github/spec-kit
