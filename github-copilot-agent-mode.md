# GitHub Copilot: Agent Mode Best Practices for Feature Development

Best practices for developing features using GitHub Copilot agent mode—faster iteration, fewer back-and-forths, and better results.

---

## 1. When to use Agent mode vs Ask / Edit

Use the mode that matches how much autonomy you want and how many files/steps are involved.

| Mode | Use when | Agent does |
| ---- | -------- | ---------- |
| **Ask** | Quick questions, explanations, or single-snippet suggestions | Answers in chat; you copy or apply. |
| **Edit** | Targeted changes in one or a few places | Suggests edits in place; you accept or tweak. |
| **Agent** | Multi-file work, feature implementation, refactors, tests + code | Plans, reads files, edits, runs commands; you review and approve. |

Prefer **Agent** for feature work that spans multiple files, tests, or build steps. Use Ask/Edit for small, localized changes.

---

## 2. Scoping tasks for Agent mode

Well-scoped tasks reduce hallucination and wasted iterations. Give the agent a clear problem and outcome.

| Element | Include | Avoid |
| ------- | ------- | ----- |
| **Problem** | What’s wrong or what’s missing (e.g. “Checkout doesn’t apply promo codes”) | Vague (“make it better”, “fix the bug”) |
| **Scope** | Which area or files (e.g. “cart and checkout API”) | “Everywhere” or unspecified scope |
| **Acceptance** | Concrete done criteria (e.g. “Discount appears in total; tests pass”) | Implicit or moving targets |
| **Constraints** | Patterns, APIs, or “don’t change X” if relevant | Assuming the agent knows project-only rules |

Start with one clear feature or fix. If the work is large, break it into ordered steps and hand the agent one step at a time.

---

## 3. Prompting for feature development

Prompts that work well with agent mode are specific and actionable.

| Practice | Do | Avoid |
| -------- | -- | ------ |
| **Context** | Mention the tech (e.g. “React”, “Django”) and relevant file/component names | Assuming the agent has full codebase context |
| **Format** | “Add X that does Y; follow existing Z pattern” | Long essays; multiple unrelated asks in one prompt |
| **Examples** | Point to a similar feature or file: “Like in `AuthForm.tsx`” | No reference when the project has strong conventions |
| **Output** | “Add unit tests for the new function” or “Update the API doc” | Leaving testing or docs implicit |

For big features, give a short step list in the first message (e.g. “1) Add endpoint, 2) Wire UI, 3) Add tests”) so the agent can work through it.

---

## 4. Iterating and reviewing

Agent mode works best when you steer and verify; don’t accept large changes blindly.

| Practice | Do | Avoid |
| -------- | -- | ------ |
| **Review** | Glance at diffs before accepting; run tests/lint after agent edits | Accepting all changes without reading |
| **Corrections** | Reply with a short, concrete fix (“Use `userId` not `id` in the query”) | Vague “this is wrong” without saying what to change |
| **Batching** | In PRs, use “Start a review” and @copilot once with a list of requested changes | Many separate comments that each trigger a new agent run |
| **Rollback** | Commit or stash before big agent runs so you can revert easily | Irreversible edits without a checkpoint |

Treat the agent as a fast first draft; you own the final behavior and style.

---

## 5. Project-level guidance (custom instructions / docs)

Help the agent stay aligned with your repo and patterns.

| Lever | Use for | Example |
| ----- | -------- | ------- |
| **Repo instructions** | Stack, structure, and conventions | “We use Tailwind; components live under `src/components`.” |
| **README / CONTRIBUTING** | Setup, test commands, and PR expectations | “Run `pnpm test` and `pnpm lint` before committing.” |
| **MCP (if available)** | External tools, APIs, or data the agent needs | Docs, runbooks, or tooling the agent can call via MCP |

Keep instructions short and stable. Put detailed runbooks or ops procedures in a separate doc (e.g. IT operations) and link or reference them when relevant.

---

## 6. Analysis: Custom instructions, prompts, and skill-like patterns

Agent mode uses several levers to steer behavior. Understanding when each applies avoids duplication and keeps the agent consistent.

### What each lever is and when it applies

| Lever | What it is | When the agent uses it | Scope |
| ----- | ---------- | ----------------------- | ----- |
| **Repository custom instructions** | `.github/copilot-instructions.md` | Every Copilot request in the repo (chat, edit, agent) | Whole repo |
| **Path-specific instructions** | `.github/instructions/NAME.instructions.md` (path rules) | When the request touches files matching the path | Per path/area |
| **Agent instructions** | `AGENTS.md`, `CLAUDE.md`, or `GEMINI.md` | When the agent plans or acts (agent mode, coding agent) | Repo or subdir |
| **Your prompt** | What you type in the chat or agent box | That single conversation turn | One task |
| **Prompt files / saved prompts** | Reusable markdown or text you @-mention or paste | When you explicitly reference them | Per use |
| **MCP (Model Context Protocol)** | Servers that expose tools or data to the agent | When the agent calls a tool or reads a resource you've connected | Per integration |

- **Custom instructions** = always-on context the agent gets without you retyping it. Use for rules that must apply to most or all requests.
- **Prompts** = task-specific. Use for "do this now" (one feature, one bug, one refactor). Good prompts reference project conventions that may already be in custom instructions.
- **Skill-like patterns** = in Copilot you don't have a separate "SKILL" file type, but you get similar effects with: (1) path-specific instructions for domain areas (e.g. "when editing under `packages/api/`, follow our API contract rules"), (2) saved prompt files or snippets for recurring workflows, (3) MCP for external context (tickets, runbooks, APIs).

### How they layer

Custom instructions (repo and path) are merged and always in context when you're in that repo/path. Your prompt adds the concrete task. Agent instructions add behavior rules specifically for agent mode. MCP adds extra tools or data when configured. So: **base behavior from instructions** + **task from your prompt** + **optional tools/data from MCP**.

---

### Use cases: When to use which

| Goal | Use | Example |
| ---- | --- | ------- |
| Enforce stack and structure for the whole repo | Repository custom instructions | "We use Next.js App Router. API routes live under `app/api/`. Use server components by default." |
| Different rules per area (e.g. API vs UI) | Path-specific instructions | `api.instructions.md`: "All endpoints return JSON; use our `apiResponse()` helper. No UI code here." |
| Guide how the agent plans and acts | Agent instructions (`AGENTS.md`) | "When implementing a feature: 1) Update types first, 2) Implement, 3) Add tests. Never commit without running the test script." |
| One-off feature or fix | A clear prompt in chat/agent | "Add a `GET /users/me` endpoint that returns the current user from the session; follow the pattern in `auth.ts`." |
| Reusable workflow (e.g. "add a new API route") | Saved prompt file or snippet you paste | A short checklist you @-mention or paste: "New API route: 1) Add route under `app/api/…`, 2) Use `apiResponse`, 3) Add Zod schema, 4) Add test." |
| Bring external context into the agent | MCP | Connect a docs server or runbook tool so the agent can read internal docs when answering ops or API questions. |
| Code review consistency | Repository or path custom instructions | "In PRs, flag any new endpoint that doesn't have a test. Prefer our `describeApiRoute()` helper." |
| Onboard new contributors | README + repo custom instructions | README: setup and commands; `copilot-instructions.md`: "We use Remix; loaders in `*.loader.ts`, actions in `*.action.ts`." |

### Practical takeaways

- **Put "always true" rules in custom instructions** (repo or path). Put "do this specific task" in your prompt.
- **Use path-specific instructions like "skills"** for domains: e.g. one "skill" for API layer, one for UI, one for tests, each in its own `.instructions.md` keyed to paths.
- **Keep instructions short and scannable.** Copilot code review may only use the first 4,000 characters of instruction files; Chat and agent can use more, but dense bullets and tables still help.
- **Prompts that reference instructions work best.** e.g. "Add a new API route; follow our API instructions" so the agent uses both your task and the path instructions.

---

## 7. What to avoid in Agent mode

Some work is a poor fit for agent mode; use Ask/Edit or do it yourself.

| Situation | Prefer | Reason |
| --------- | ------ | ------ |
| Learning a new codebase or concept | Ask mode or manual reading | Agent may change code you don’t yet understand. |
| Vague or disputed requirements | Clarify first, then Agent | Agent will guess; rework is costly. |
| Security-sensitive or critical production changes | Manual review and change | One wrong edit can have high impact. |
| Large, ambiguous refactors | Small, scoped refactor steps | Agent works better on clear, bounded tasks. |
| Purely exploratory “try things” | Ask or small Edit | Agent is best when the goal is clear. |

When in doubt, start with a tiny slice of the feature and expand once the agent’s output looks good.

---

## 8. Quick reference: feature-development flow

| Step | Action |
| ---- | ------ |
| 1 | Scope one feature or fix; write 1–2 sentences of acceptance criteria. |
| 2 | Open Agent mode; paste the task and, if useful, a short step list. |
| 3 | Let the agent plan and edit; watch the changes and read key diffs. |
| 4 | Run tests and lint; reply with concrete fixes if something’s wrong. |
| 5 | Commit only after you’re satisfied; use small commits for easier history. |

For runbooks, infra changes, and ops tasks, see [copilot-agent-it-operations.md](copilot-agent-it-operations.md).
