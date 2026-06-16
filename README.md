# Agent Skills for Safe AI App Building

A small collection of **Agent Skills** that keep AI-built apps safe to edit. Each skill is a focused `SKILL.md` playbook that an AI coding agent loads on demand and follows for a specific kind of task — so the agent makes the change you asked for without quietly breaking security, data, or tests that already work.

Built for [Lovable](https://lovable.dev), and portable to any tool that supports the Agent Skills convention (Claude, and others that read the same `SKILL.md` shape).

---

## Why these exist

AI coding agents are generalists. They are good at producing code, but on an existing app they tend to:

- touch unrelated files and break working features while fixing one thing,
- forget to enable row-level security on a new table,
- ship an endpoint without auth or a rate limit,
- send personal data to a model, or weaken a safety prompt,
- run destructive schema changes,
- and delete or skip failing tests to make a build "pass."

Each skill below encodes one guardrail against one of those failure modes. Skills load only when a request matches their description, so you can keep all of them installed without bloating every prompt.

---

## The skills

| Skill | What it enforces | Triggers when you… | Scope |
|---|---|---|---|
| **surgical-edits** | The smallest correct change; never refactor or touch unrelated code | Ask for a scoped change — one bug, one component, copy, styling | Any stack |
| **keep-tests-green** | Update/add tests with changed logic and run them; never delete or skip a test to pass | Change logic that has (or should have) test coverage | Any stack |
| **supabase-rls-guard** | Row-level security on every table, owner-scoped access, a single role-check pattern, service key server-only | Add or change a table, policy, or migration | Supabase |
| **edge-function-contract** | CORS, JWT auth, input validation, per-user rate limiting, secrets server-side | Create or edit a Supabase edge function | Supabase |
| **safe-migrations** | Block destructive schema changes without confirmation; idempotent, reversible migrations; RLS on new tables | Write or edit a SQL migration / schema change | Supabase |
| **ai-feature-safety** | Redact PII before the model, preserve crisis/safety guardrails, don't log raw content, bound cost | Build or edit an AI feature or a system prompt | AI features |

Two of these (`surgical-edits`, `keep-tests-green`) apply to any project. The other four assume a Supabase backend and/or an AI feature; read each skill before installing and adjust the specifics to match your stack.

---

## Repository structure

Each skill lives in its own folder with a `SKILL.md` at the folder root. This is the layout Lovable expects when importing a single skill from a subdirectory.

```
.
├── README.md
├── surgical-edits/
│   └── SKILL.md
├── keep-tests-green/
│   └── SKILL.md
├── supabase-rls-guard/
│   └── SKILL.md
├── edge-function-contract/
│   └── SKILL.md
├── safe-migrations/
│   └── SKILL.md
└── ai-feature-safety/
    └── SKILL.md
```

A skill is just a Markdown file with YAML frontmatter:

```markdown
---
name: skill-name
description: Use when … (the trigger and boundary the agent uses to decide whether to load it)
---

# Instructions the agent follows once the skill is loaded
```

---

## Install in Lovable

You need to be a **workspace owner or admin** to add custom skills. Skills are shared across every project in the workspace.

### Option A — Import from GitHub (recommended for this repo)

Because this repo holds **multiple** skills, import each one by its **subdirectory** URL. In Lovable go to **Settings → Skills → Add → Import from GitHub** and paste, e.g.:

```
https://github.com/OWNER/REPO/tree/main/supabase-rls-guard
https://github.com/OWNER/REPO/tree/main/edge-function-contract
https://github.com/OWNER/REPO/tree/main/ai-feature-safety
https://github.com/OWNER/REPO/tree/main/safe-migrations
https://github.com/OWNER/REPO/tree/main/surgical-edits
https://github.com/OWNER/REPO/tree/main/keep-tests-green
```

Replace `OWNER/REPO` and `main` with your repo and default branch. The linked folder must contain `SKILL.md` directly (it does). Repeat for each skill you want.

### Option B — Upload a ZIP

Zip any skill folder so the archive contains `skill-name/SKILL.md`, then **Settings → Skills → Add → Upload ZIP**.

### Option C — Write manually

**Settings → Skills → Add → Write manually**, then copy from the skill's `SKILL.md`:

- **Name** and **Description** from the frontmatter block.
- **Content** = everything below the closing `---`.

---

## Using a skill

Once installed, a skill works two ways:

- **Automatically** — when your request matches the skill's description, the agent applies it. You don't do anything special.
- **On demand** — type `/` in chat, pick the skill, then write your request. The skill becomes the *how*; your prompt is the *what*. Example: `/supabase-rls-guard add a notifications table for the current user`.

Skills compose. A single request can pull in several at once — for example, adding an authenticated AI endpoint that writes to a new table can engage `edge-function-contract`, `supabase-rls-guard`, and `ai-feature-safety` together.

---

## Portability

These follow the Agent Skills convention, so the same `SKILL.md` files work in other tools that support it. You can download a skill from Lovable as a `.zip` and upload it to Claude (or vice versa); the name, description, and instructions carry over unchanged.

---

## Customizing

These are playbooks, not magic. Open any `SKILL.md` and:

- tighten the **description** so it triggers on your real workflows and not on unrelated tasks,
- replace stack-specific details (table-naming, role checks, model/provider, test runners) with your own,
- keep each skill to **one job** — split anything that tries to do too much.

A vague description is the main reason a skill fails to load when expected; lead with a concrete "Use when…" and state what it's *not* for.

---

## Contributing

Issues and PRs welcome. When adding a skill: one job per skill, a sharp `Use when…` description with explicit boundaries, and concrete instructions a new teammate could follow on their first day.

---

## License

Add your license of choice (e.g. MIT) as a `LICENSE` file.
