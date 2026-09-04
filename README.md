# project-audit

A comprehensive, evidence-tagged audit of any software project: architecture and code, security and threat model, supply chain and licensing, testing, performance, operations and DR, data governance, docs and DevEx, the business logic that was decided, dev workflow and git forensics, decision history, business/commercial inference, cloud cost, legal/compliance, AI/LLM risk, accessibility and RTL, legacy modernization, project-type checks, change-level (PR) review, and open-source community health — 21 topics, each in its own file, run by one checkpointed sub-agent per topic so a crash never loses finished work.

## How a run works
1. **Intake (always asked first).** The skill detects the mode (self-audit / due-diligence / onboarding / pr-review), lists which inputs it can see, shows the 21-topic menu with the recommended preset ticked and every auto-skip explained, and waits for `go`, `all`, or `add/remove <IDs>`.
2. **Foundation pass.** A fast inventory of the repo (identity card, git snapshot, applicability flags, pointers per topic) written to `audit-run/<timestamp>/01-foundation.md`.
3. **Topic sub-agents.** One sub-agent per selected topic, each writing its own `topics/T##-*.md` file *as it works* (findings appended the moment they are established, progress header updated after every section). If a sub-agent times out or crashes, the orchestrator re-dispatches it with `RESUME` and it continues from the file — nothing done is redone or lost.
4. **Synthesis and report.** Cross-cutting findings, scorecard (1–5 per topic), risk register (impact × likelihood × ease), recommendations (quick wins / short-term / strategic), open questions, and — in due-diligence mode — the list of things to request from the team beyond the repo.

Every claim is tagged `[Observed]` (with file/line/commit), `[Inferred]` (with its evidence chain), or `[Speculative]`, with a confidence level.

## Install
- **Claude Code** — copy this folder to `.claude/skills/project-audit/` in the repo (or `~/.claude/skills/project-audit/` for all repos). Optional: create `.claude/agents/audit-topic.md` from section 12 of `references/core/orchestration-and-checkpoints.md`.
- **claude.ai / Cowork** — upload `project-audit.skill` and click **Save skill**.
- **Other Agent-Skills-compatible tools** — the folder follows the open Agent Skills format (SKILL.md + references/ + assets/); point the tool at the folder.

## Use
Say any of: "audit this project", "comprehensive review of this repo", "technical due diligence on <repo>", "onboard me to this codebase", "review this PR against the spec" — or in Hebrew: "ביקורת מקיפה על הפרויקט", "ניתוח פרויקט", "בדיקת נאותות". Answer the intake message, then let it run. The report is written in your language; identifiers and paths stay in English.

- **No local terminal?** Pack the repo with Repomix (`npx repomix --remote owner/repo --compress`, or repomix.com) or gitingest and upload the single file; or give the GitHub URL and the skill uses the web UI and Insights with `[Inferred]` tags for approximations.
- **Resume a run.** If an `audit-run/<timestamp>/00-manifest.md` exists, the skill offers to resume; only unfinished topics are re-dispatched.
- **Results.** `audit-run/<timestamp>/03-report.md` is the deliverable; the topic files are the audit trail. Add `audit-run/` to `.gitignore` if the run directory lives inside the repo.

## Structure
```
project-audit/
├── SKILL.md                                  # foundation pass + orchestration (the one part every run uses)
├── README.md
├── references/
│   ├── core/
│   │   ├── orchestration-and-checkpoints.md  # run dir, manifest, checkpoint contract, recovery, waves, environments
│   │   ├── evidence-and-scoring.md           # tags, confidence, severity, priority, scorecard, deal-repricing checks
│   │   └── report-template.md                # full and short report formats
│   └── topics/                               # T01 … T21 — one self-contained checklist per topic
└── assets/templates/                         # manifest.md · topic-file.md · subagent-brief.md
```

---

# project-audit (עברית)

סקיל לביקורת מקיפה של פרויקט תוכנה, מתויג-ראיות: ארכיטקטורה וקוד, אבטחה ומידול איומים, שרשרת אספקה ורישוי, בדיקות, ביצועים, תפעול ושחזור מאסון, ממשל נתונים, תיעוד וחוויית מפתח, הלוגיקה העסקית שנקבעה, תהליכי פיתוח וניתוח היסטוריית git, ארכאולוגיית החלטות, הסקה עסקית-מסחרית, עלויות ענן, משפט ורגולציה, סיכוני בינה מלאכותית, נגישות ו-RTL, מודרניזציה של קוד ישן, בדיקות לפי סוג פרויקט, סקירת שינוי בודד (PR), ובריאות קהילת קוד פתוח — 21 נושאים, קובץ לכל נושא, וסוכן משנה לכל נושא עם תיעוד שוטף כך שנפילה של סוכן לא מוחקת עבודה שהושלמה.

## איך ריצה עובדת
1. **שאלת פתיחה (תמיד קודם).** הסקיל מזהה את המצב (ביקורת עצמית / בדיקת נאותות / היכרות / סקירת PR), מציג אילו קלטים זמינים, מציג את תפריט 21 הנושאים עם ברירת המחדל המומלצת וכל דילוג אוטומטי מוסבר — וממתין לתשובה: `go`, `all`, או `add/remove <IDs>`.
2. **מעבר יסוד.** מיפוי מהיר של המאגר (כרטיס זהות, תמונת git, דגלי ישימות, נקודות פתיחה לכל נושא) נכתב ל-`audit-run/<timestamp>/01-foundation.md`.
3. **סוכני משנה לפי נושא.** סוכן לכל נושא שנבחר, כותב לקובץ משלו `topics/T##-*.md` תוך כדי עבודה (כל ממצא נרשם ברגע שנקבע, כותרת ההתקדמות מתעדכנת אחרי כל סעיף). אם סוכן נופל או נגמר לו הזמן, המתזמר מריץ אותו שוב עם `RESUME` והוא ממשיך מהקובץ — שום דבר שהושלם לא חוזר ולא נמחק.
4. **סינתזה ודוח.** ממצאים חוצי-נושאים, ציונים (1–5 לנושא), מרשם סיכונים (השפעה × סבירות × קלות תיקון), המלצות (רווחים מהירים / טווח קצר / אסטרטגי), שאלות פתוחות, ובמצב בדיקת נאותות — רשימת מה לבקש מהצוות מעבר למאגר.

כל טענה מתויגת `[Observed]` (עם קובץ/שורה/קומיט), `[Inferred]` (עם שרשרת הראיות) או `[Speculative]`, עם רמת ביטחון.

## התקנה
- **Claude Code** — להעתיק את התיקייה ל-`.claude/skills/project-audit/` במאגר (או `~/.claude/skills/project-audit/` לכל המאגרים).
- **claude.ai / Cowork** — להעלות את `project-audit.skill` וללחוץ **Save skill**.

## שימוש
לכתוב: "ביקורת מקיפה על הפרויקט", "ניתוח פרויקט", "בדיקת נאותות על <repo>", "תעשה לי היכרות עם הקוד הזה", "סקור את ה-PR מול המפרט". לענות על שאלת הפתיחה ולתת לו לרוץ. הדוח נכתב בשפה שלך; מזהים ונתיבים נשארים באנגלית.

- **בלי טרמינל מקומי (עבודה מהנייד)?** לארוז את המאגר עם Repomix (‏`npx repomix --remote owner/repo --compress` או repomix.com) או gitingest ולהעלות קובץ אחד; או לתת קישור GitHub — הסקיל ישתמש בממשק הווב וב-Insights ויתייג הערכות כ-`[Inferred]`.
- **המשך ריצה.** אם קיים `audit-run/<timestamp>/00-manifest.md`, הסקיל מציע להמשיך; רק נושאים שלא הסתיימו נשלחים שוב.
- **תוצאות.** `audit-run/<timestamp>/03-report.md` הוא הדוח; קבצי הנושאים הם שובל הביקורת. להוסיף `audit-run/` ל-`.gitignore` אם תיקיית הריצה בתוך המאגר.
