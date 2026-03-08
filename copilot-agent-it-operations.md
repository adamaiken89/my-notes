# GitHub Copilot Agent Mode: IT Operations

Using GitHub Copilot agent mode for IT operations—runbooks, infrastructure, debugging, and automation—with clear boundaries and safety.

---

## 1. When Agent mode helps for IT ops

Agent mode can draft scripts, suggest commands, and align changes with runbooks or docs. Use it where the task is well-defined and reversible.

| Use case | Agent can help with | You should |
| -------- | ------------------- | ---------- |
| **Runbooks** | Draft or update steps; suggest commands from docs | Verify commands and order; run in a safe environment first. |
| **Scripts** | Write or adjust automation (shell, Python, etc.) | Review for safety (no prod secrets, destructive ops). |
| **Config / IaC** | Propose config or Terraform/CloudFormation edits | Validate and apply via your change process. |
| **Debugging** | Suggest checks and commands from logs or errors | Run only what you understand; avoid blind paste in prod. |
| **Docs / playbooks** | Keep runbooks and playbooks consistent with tooling | Own approval; ensure steps match real systems. |

Avoid using the agent to execute commands or make changes in production on your behalf; it should suggest, you should decide and run.

---

## 2. Scoping ops tasks

Ops work often touches many systems; narrow the scope so the agent’s output is reviewable and safe.

| Element | Include | Avoid |
| ------- | ------- | ----- |
| **Environment** | “Staging”, “dev”, or “local only” when relevant | Implying production without saying so |
| **Goal** | One clear outcome (e.g. “Script to restart app and run health check”) | “Fix everything” or multiple unrelated goals |
| **Constraints** | “No direct DB writes”, “read-only”, “must be idempotent” | Assuming the agent knows your safety rules |
| **References** | Links or paths to runbooks, existing scripts, or docs | Expecting the agent to know internal-only procedures |

Break large ops tasks (e.g. “migrate X”) into small steps and hand the agent one step at a time.

---

## 3. Runbooks and playbooks

Use agent mode to create or update runbooks, not to execute them unsupervised.

| Practice | Do | Avoid |
| -------- | -- | ------ |
| **Structure** | Clear steps: precondition, command/action, expected result, rollback | Long prose; missing “what to do if it fails” |
| **Commands** | Ask the agent to suggest commands; you paste into runbook after review | Copying unverified commands into official runbooks |
| **Placeholders** | Use clear placeholders (e.g. `{{ HOST }}`, `{{ ENV }}`) for env-specific values | Hardcoding hosts, accounts, or secrets |
| **Links** | Reference external docs or dashboards by URL | Relying on the agent to know internal URLs |

Keep runbooks in repo or a known path; tell the agent “update runbook at X” so it can read and propose edits. For execution, a human (or a controlled pipeline) should run the steps.

---

## 4. Infrastructure and config changes

Agent mode can propose IaC or config edits; you own validation and promotion.

| Area | Agent role | Your role |
| ---- | ---------- | ---------- |
| **Terraform / CloudFormation** | Draft or adjust resources and variables from a short spec | Review diff; run plan/apply in non-prod first. |
| **Config files** | Suggest changes (e.g. nginx, systemd) from “we need X” | Check syntax and semantics; deploy via your process. |
| **Secrets** | Never put real secrets in prompts or in suggested config | Use a secrets manager; reference by name or env var. |

Always run `terraform plan` (or equivalent) and review the plan before applying. Do not let the agent run apply or push to production.

---

## 5. Debugging and diagnostics

Use the agent to suggest diagnostic steps and commands; you run them and interpret results.

| Practice | Do | Avoid |
| -------- | -- | ------ |
| **Context** | Paste error messages, log snippets, or “we see X in dashboard” | “It’s broken” with no detail |
| **Scope** | “Suggest commands to check disk and memory on Linux” | “Fix the server” |
| **Execution** | Run suggested commands yourself in the right environment | Pasting agent-suggested commands into prod without review |
| **Sensitivity** | Use redacted or sample logs when possible | Pasting secrets, tokens, or PII into the chat |

Treat agent output as a draft runbook for the current incident; validate and then run.

---

## 6. Safety and access

Keep ops use of agent mode within your security and change-management policies.

| Topic | Recommendation |
| ----- | ---------------- |
| **Credentials** | Never put real credentials or long-lived tokens in prompts or in code the agent writes. |
| **Permissions** | Run agent-suggested commands with the least privilege needed; avoid root in prod. |
| **Production** | No direct production changes from agent output without human review and approval. |
| **Audit** | Prefer executing from scripts/runbooks in version control so changes are auditable. |
| **MCP / integrations** | If using MCP for ops tools, limit scope and permissions; prefer read-only where possible. |

When in doubt, use the agent to generate or update runbooks and scripts, then execute them through your normal change process.

---

## 7. Quick reference: ops flow with Agent mode

| Step | Action |
| ---- | ------ |
| 1 | Define one ops task with environment, goal, and constraints. |
| 2 | Point the agent at existing runbooks or scripts if they exist. |
| 3 | Ask for suggested steps, commands, or code; review everything. |
| 4 | Test in non-prod (or dry-run) before using in production. |
| 5 | Update runbooks/scripts in repo with the validated steps; never commit secrets. |

For feature development and general agent-mode practices, see [github-copilot-agent-mode.md](github-copilot-agent-mode.md).
