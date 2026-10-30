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

1. افتح Claude Code جوه فولدر المشروع (جديد أو موجود).
2. اكتبله طلبك عادي وقول إنك عايز تستخدم Spec-Kit، مثلًا:

   ```
   عايز أبني مشروع متجر صغير بـ Laravel + Blade باستخدام speckit
   ```

3. السكيل هيشتغل لوحده: يتأكد من الأدوات، ينفّذ `specify init --here --integration claude`، وبعدين يمشّيك في الخطوات:

| # | الأمر | بيعمل إيه |
|---|---|---|
| 1 | `/speckit.constitution` | مبادئ المشروع (قواعد الكود، الاختبارات، الأمان) |
| 2 | `/speckit.specify` | وصف الـ feature ← `specs/001-name/spec.md` |
| 3 | `/speckit.clarify` | (اختياري ومهم) يسألك عن أي حاجة مش واضحة |
| 4 | `/speckit.plan` | الخطة التقنية والـ stack والـ data model |
| 5 | `/speckit.checklist` | مراجعة جودة المتطلبات (UX، RTL، accessibility) |
| 6 | `/speckit.tasks` | تقسيم الخطة لمهام صغيرة |
| 7 | `/speckit.analyze` | (اختياري) يتأكد إن الـ spec والـ plan والـ tasks متطابقين |
| 8 | `/speckit.implement` | ينفّذ المهام ويكتب الكود |

> بعد `specify init` ممكن تحتاج تقفل Claude Code وتفتحه تاني عشان أوامر `/speckit.*` تظهر. حسب نسخة Spec-Kit، الأوامر ممكن تظهر بالشكل `/speckit.specify` أو `/speckit-specify` — الاتنين نفس الأمر.

**نصايح:**
- كل feature (تسجيل دخول، منتجات، سلة...) ليها `/speckit.specify` لوحدها وفولدر مرقّم في `specs/`.
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

Open Claude Code in your project folder and ask for what you want to build "using speckit". The skill takes it from there. You may need to restart Claude Code after `specify init` for the `/speckit.*` commands to appear.

## ChatGPT

This skill is built for Claude Code's skills/plugins system. There's no direct install for ChatGPT.

## License

[MIT](LICENSE) — Mamdouh.
