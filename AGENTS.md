# AGENTS.md

Guidance for AI agents working on the Agent37 docs. `CLAUDE.md` imports this file via `@AGENTS.md`, so this is the single source of truth — edit here, not there.

## About this project

- The public documentation for **Agent37 Cloud**, built on [Mintlify](https://mintlify.com) and published at `https://www.agent37.com/docs`.
- Pages are MDX files with YAML frontmatter; navigation, theme, and redirects live in `docs.json`.
- Preview locally with `mint dev` (`npm i -g mint`); run `mint broken-links` before pushing. Publishing is automatic from `main` via the Mintlify app — there is no CI in this repo.
- Mintlify auto-generates `llms.txt` and `llms-full.txt` at publish. **Customers' coding agents and our examples repo build clients straight from `…/docs/llms-full.txt`** — the reference is consumed as a spec, so precision beats prose.
- For Mintlify product knowledge (components, configuration), install the Mintlify skill: `npx skills add https://mintlify.com/docs`.

## What the docs describe

Two API planes, one `sk_live_` key — the navigation mirrors this split:

- **Hosting API** (`https://api.agent37.com/v1`) — manage instances: instances, templates, exec, budgets, plus billing and errors reference.
- **Agent API** (`https://{instanceId}.agent37.app/v1`) — talk to one instance's agent: chat (responses), streaming, sessions, files.

The API reference is **hand-authored MDX** — there is no OpenAPI spec to regenerate from. Endpoint pages use `<ParamField>`/`<ResponseField>` and show curl, Python, and Node examples. If a page is renamed or moved, add a redirect in `docs.json` so old URLs keep working (several exist already).

## Accuracy rules

- The platform is pre-launch and moves fast; the implementation is the source of truth and these docs are hand-written, so drift is the failure mode. **Verify behavior against the live API before documenting it** — never document from memory or from an example app.
- An API change and its docs change ship together. "Update the docs later" is how the reference rots.
- Pricing, limits, ids, and resource shapes in the docs are load-bearing — they're quoted to customers and parsed by coding agents. Change them only to match a real platform change.

## Terminology

Use these exactly; consistent terms are what make `llms-full.txt` usable as a spec.

- **Agent37 Cloud** — the product. **Workspace** — the account/billing unit; owns API keys and one **wallet**.
- **Instance** — an agent's always-on computer at `https://{instanceId}.agent37.app`. **Session** — one conversation on an instance. **Response** — one agentic turn within a session.
- **Agent** vs **model**: the agent is the software running on the instance (Hermes today; OpenClaw, Claude Code, Codex coming soon); the model is the LLM chosen per turn (`model` + `provider`).
- **Template** — names the image an instance runs and declares its ports. **Gateway** — serves the Agent API inside the instance.
- Billing: **wallet** (workspace money), **budget** (per-instance managed-spend cap plus **top-up** headroom), **managed services** (LLM / search / Composio through built-in credentials), **micros** (USD × 1,000,000), **past due** (renewal failed; instance force-stopped until a funded start).
- Conventions: instance ids are 10-char lowercase alphanumeric; session/response ids are 32-char hex; Hosting API timestamps are epoch **seconds**, Agent API timestamps epoch **milliseconds**; list endpoints wrap results in `{ "data": [...] }`, newest first.

## Content boundaries

- Document **only the two public planes**. The dashboard, internal/admin APIs, and fleet/host architecture are out of scope.
- The OpenClaw setup guides (`openclaw/`, `channels/`, `models/`, `runtime/`, `networking/`, `tailscale/`) are intentionally hidden from the nav but searchable — they serve existing OpenClaw customers. Don't surface them in the Agents API nav and don't delete them.
- The system template catalog has one entry, `agent37-hermes`; the coming-soon agents (OpenClaw, Claude Code, Codex) are absent from the API entirely — mention them as coming, never as available.
- Drafts go in `drafts/` or `*.draft.mdx` (ignored via `.mintignore`).

## Style

- Active voice, second person ("you"); one idea per sentence; sentence case for headings.
- Bold for UI elements: Click **Settings**. Code formatting for file names, commands, paths, endpoints, and field names.
- Lead with the goal; prefer a runnable example over describing one.
