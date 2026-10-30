<div dir="rtl">

# Spec-Kit Skill لـ Claude Code

سكيل (Skill) بيسهّل استخدام [GitHub Spec-Kit](https://github.com/github/spec-kit) جوه **Claude Code**.

بدل ما تحفظ أوامر Spec-Kit وترتيبها، Claude بيعمل الآتي:
- يتأكد إن الأدوات متسطّبة، ولو ناقصة يسطّبها.
- يجهّز المشروع بـ `specify init`.
- يمشّيك في الخطوات بالترتيب: **constitution → specify → clarify → plan → checklist → tasks → analyze → implement**.
- يقف بعد كل مرحلة مهمة (الـ spec، الـ plan، الـ checklist) ويعرض عليك ملخص تراجعه قبل ما يكمّل، ومش بيبدأ يكتب كود قبل ما توافق على الـ spec والـ plan.

> السكيل ده مكمّل لأداة Spec-Kit الرسمية من GitHub، مش بديل ليها. الأداة نفسها لازم تتسطّب على جهازك (الخطوات تحت).

---

## 1. المتطلبات (لازم تكون على جهازك)

| الأداة | لازمتها إيه |
|---|---|
| **Claude Code** | السكيل شغال جواه |
| **Git** | أداة `specify` بتتسطّب من GitHub |
| **uv** | مدير أدوات Python، بيسطّب `specify` (وبينزّل Python لنفسه لو مش موجود) |
| **specify-cli** | أداة Spec-Kit نفسها |

### على Mac

افتح **Terminal** ونفّذ:

```bash
# 1) Git — لو مش متسطّب
xcode-select --install

# 2) Claude Code
curl -fsSL https://claude.ai/install.sh | bash

# 3) uv
curl -LsSf https://astral.sh/uv/install.sh | sh
```

اقفل الـ Terminal وافتحه تاني، وبعدين:

```bash
# 4) specify-cli
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

### على Windows

افتح **PowerShell** ونفّذ:

```powershell
# 1) Git — لو مش متسطّب
winget install --id Git.Git -e

# 2) Claude Code
irm https://claude.ai/install.ps1 | iex

# 3) uv
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

اقفل الـ PowerShell وافتحه تاني، وبعدين:

```powershell
# 4) specify-cli
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
```

### اتأكد إن كله تمام

```bash
git --version
claude --version
uv --version
specify --version
```

لو `specify` طلع "not found": نفّذ `uv tool update-shell` وافتح الـ terminal من جديد.

---

## 2. تسطيب السكيل على Claude Code

### الطريقة الأولى (المفضّلة): كـ Plugin

افتح Claude Code (اكتب `claude` في الـ terminal) ونفّذ:

```
/plugin marketplace add mamdouh19952/speckit-claude-skill
/plugin install speckit@speckit-claude-skill
```

وبعدين اقفل Claude Code وافتحه تاني. عشان تجيب أحدث نسخة بعد كده:

```
/plugin marketplace update speckit-claude-skill
```

### الطريقة التانية: نسخ يدوي

انسخ مجلد `plugins/speckit/skills/speckit-workflow` من الريبو ده لواحد من المكانين دول:

| النطاق | Mac | Windows |
|---|---|---|
| كل مشاريعك | `~/.claude/skills/speckit-workflow/` | `%USERPROFILE%\.claude\skills\speckit-workflow\` |
| مشروع واحد بس | `<project>/.claude/skills/speckit-workflow/` | `<project>\.claude\skills\speckit-workflow\` |

مثال على Mac:

```bash
git clone https://github.com/mamdouh19952/speckit-claude-skill.git
mkdir -p ~/.claude/skills
cp -R speckit-claude-skill/plugins/speckit/skills/speckit-workflow ~/.claude/skills/
```

مثال على Windows (PowerShell):

```powershell
git clone https://github.com/mamdouh19952/speckit-claude-skill.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills"
Copy-Item -Recurse speckit-claude-skill\plugins\speckit\skills\speckit-workflow "$env:USERPROFILE\.claude\skills\"
```

---

## 3. طريقة الاستخدام

افتح Claude Code جوه فولدر المشروع، واكتب طلبك عادي وقول إنك عايز تستخدم speckit. السكيل بيعرف لوحده إنت في أنهي حالة من التلاتة دول:

### الحالة 1: مشروع جديد (فولدر فاضي)

```
عايز أبني متجر صغير بـ Laravel + Blade باستخدام speckit
```

Claude هيعمل:
1. `specify init` (يجهّز `.specify/` وأوامر Spec-Kit).
2. `/speckit-constitution`: تتفقوا على مبادئ المشروع.
3. أول feature: specify ← clarify ← plan ← checklist ← tasks ← implement.

### الحالة 2: مشروع موجود، وأول مرة تستخدم فيه Spec-Kit

```
المشروع ده شغال، عايز أضيف feature للتقارير الشهرية باستخدام speckit
```

Claude هيعمل:
1. يقرا الكود الموجود. ولو مفيش `CLAUDE.md` هيقترح عليك تشغّل `/init` الأول عشان يفهم المشروع (اختياري بس مفيد).
2. `specify init`: آمن على المشروع، ومش بيلمس الكود ولا `CLAUDE.md` ولا `.claude/settings.json` (اتجرّب).
3. `/speckit-constitution`: المبادئ بتتاخد من الكود الموجود (الـ stack، التسمية، شكل الفولدرات) عشان الجديد يمشي زي القديم.
4. الـ feature الجديدة: specify ← … ← implement.

### الحالة 3: مشروع فيه Spec-Kit من قبل، وعايز تضيف feature جديدة

```
عايز أضيف feature الكوبونات باستخدام speckit
```

Claude **مش هيعمل init تاني ولا constitution تاني**، وهيبدأ على طول من `/speckit-specify`. الـ feature الجديدة بتاخد الفولدر اللي عليه الدور في `specs/` (مثلًا `specs/004-coupons/`).

### الأوامر بالترتيب

| # | الأمر | بيعمل إيه |
|---|---|---|
| 1 | `/speckit-constitution` | مبادئ المشروع، **مرة واحدة** للمشروع |
| 2 | `/speckit-specify` | وصف الـ feature ← `specs/001-name/spec.md` |
| 3 | `/speckit-clarify` | (اختياري ومهم) يسألك عن أي حاجة مش واضحة |
| 4 | `/speckit-plan` | الخطة التقنية والـ stack والـ data model |
| 5 | `/speckit-checklist` | مراجعة جودة المتطلبات (UX، RTL، accessibility) |
| 6 | `/speckit-tasks` | تقسيم الخطة لمهام صغيرة |
| 7 | `/speckit-analyze` | (اختياري) يتأكد إن الـ spec والـ plan والـ tasks متطابقين |
| 8 | `/speckit-implement` | ينفّذ المهام ويكتب الكود |

> - بعد `specify init` لو أوامر `/speckit-...` مظهرتش، اقفل Claude Code وافتحه تاني.
> - النسخ القديمة من Spec-Kit بتكتب الأوامر بنقطة (`/speckit.specify`)، والجديدة بشَرطة (`/speckit-specify`). الاتنين نفس الأمر.

### الفرق بين constitution و CLAUDE.md

| الملف | مين بيعمله | فيه إيه |
|---|---|---|
| `.specify/memory/constitution.md` | `/speckit-constitution` | مبادئ المشروع (قواعد الكود، الاختبارات، الأمان) |
| `CLAUDE.md` | Claude Code (`/init`) أو إنت | معلومات المشروع اللي Claude بيقراها كل جلسة (الـ stack، أوامر التشغيل) |

`specify init` مش بيعمل `CLAUDE.md` ومش بيعدّله، فالملفين مستقلين ومحدش فيهم بيكتب فوق التاني.

**نصايح:**
- كل feature (تسجيل دخول، منتجات، سلة...) ليها `/speckit-specify` لوحدها.
- لو مستعجل: constitution ← specify ← plan ← tasks ← implement.
- لو المشروع عربي: اذكر RTL في الـ spec، والسكيل هيشغّل checklist خاص بالـ RTL.

---

## 4. ملحوظة عن ChatGPT

السكيل ده معمول مخصوص لـ **Claude Code** (نظام الـ Skills والـ Plugins بتاعه)، وأوامر `/speckit.*` بتتولّد لـ Claude Code. مفيش طريقة تسطيب مباشرة ليه على ChatGPT.

---

## الترخيص

[MIT](LICENSE) — ممدوح.

</div>

---

# Spec-Kit Skill for Claude Code (English)

A skill that makes [GitHub Spec-Kit](https://github.com/github/spec-kit) easy to use inside **Claude Code**. Claude checks/installs the tooling, runs `specify init`, and walks you through **constitution → specify → clarify → plan → checklist → tasks → analyze → implement**, pausing for your review after the spec, plan, and checklist. It complements the official Spec-Kit CLI — it doesn't replace it.

## Requirements

| Tool | macOS (Terminal) | Windows (PowerShell) |
|---|---|---|
| Git | `xcode-select --install` | `winget install --id Git.Git -e` |
| Claude Code | `curl -fsSL https://claude.ai/install.sh \| bash` | `irm https://claude.ai/install.ps1 \| iex` |
| uv | `curl -LsSf https://astral.sh/uv/install.sh \| sh` | `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 \| iex"` |
| specify-cli | `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git` | same |

Open a new terminal after installing uv. If `specify` isn't found, run `uv tool update-shell`.

## Install the skill

**As a plugin (recommended)** — inside Claude Code:

```
/plugin marketplace add mamdouh19952/speckit-claude-skill
/plugin install speckit@speckit-claude-skill
```

**Manually** — copy `plugins/speckit/skills/speckit-workflow` into `~/.claude/skills/` (all projects) or `<project>/.claude/skills/` (one project). On Windows, `~` is `%USERPROFILE%`.

## Use it

Open Claude Code in your project folder and ask for what you want to build "using speckit". The skill handles three cases:

- **New project**: init → `/speckit-constitution` → first feature.
- **Existing project, no Spec-Kit yet**: reads the code (suggests `/init` if there's no `CLAUDE.md`) → init → constitution derived from the existing code → the new feature. Init doesn't touch your code, `CLAUDE.md`, or `.claude/settings.json`.
- **Existing project with Spec-Kit**: no init, no new constitution, straight to `/speckit-specify` for the new feature.

Claude runs `specify init --here --force --non-interactive --integration claude`. Without `--integration claude`, a non-interactive init defaults to Copilot. Restart Claude Code if the `/speckit-*` commands don't appear after init. Older Spec-Kit versions name them `/speckit.specify` and so on.

## ChatGPT

This skill is built for Claude Code's skills/plugins system. There's no direct install for ChatGPT.

## License

[MIT](LICENSE) — Mamdouh.
