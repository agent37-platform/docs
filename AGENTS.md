# AGENTS.md

Guidance for AI agents working on the Agent37 docs. `CLAUDE.md` imports this file via `@AGENTS.md`, so this is the single source of truth: edit here, not there.

## About this project

- The public documentation for **Agent37 Cloud**, built on [Mintlify](https://mintlify.com) and published at `https://www.agent37.com/docs`.
- Pages are MDX files with YAML frontmatter; navigation, theme, and redirects live in `docs.json`.
- Preview locally with `mint dev` (`npm i -g mint`); run `mint broken-links` before pushing. Publishing is automatic from `main` via the Mintlify app; there is no CI in this repo.
- Mintlify auto-generates `llms.txt` and `llms-full.txt` at publish. **Customers' coding agents and our examples repo build clients straight from `…/docs/llms-full.txt`**. The reference is consumed as a spec, so precision beats prose.
- For Mintlify product knowledge (components, configuration), the `mintlify`, `mintlify-docs`, and `mintlify-api` skills are already vendored in `.agents/skills/` and pinned in `skills-lock.json`. Refresh them with `npx skills add https://mintlify.com/docs`; there is nothing to install first.

## What the docs describe

Two API planes, one `sk_live_` key, and the reference nav mirrors that split:

- **Hosting API** (`https://api.agent37.com/v1`) manages instances: instances, templates, urls, public-ports, domains, exec, ssh, logs, metrics, budgets, integrations. Takes the key as `Authorization: Bearer sk_live_...`.
- **Agent API** (`https://{instanceId}.agent37.app/v1`) talks to one instance's agent: chat (responses), streaming, sessions, models, files, health. Takes the same key raw, no Bearer prefix, as `X-Agent37-Key: sk_live_...`; `Authorization` passes through to the software inside the instance. Every sample on this plane must use `X-Agent37-Key`, never `Authorization: Bearer`.

Two more nav groups sit above the reference, and they are not API pages:

- **Get started** (`index`, `concepts`) is the front door.
- **Build with Agent37** (`examples`, `white-label`, `composio`, `byo-model`, `chat-app`, `custom-image`, `managed-services`, `hermes-webhooks`) is task-shaped: what you can build, each page usually pointing at a forkable repo. `examples` is the table that indexes the rest, so a new guide here needs a row added there. Keep these framed around the use case; the mechanics belong in the reference pages they link to.
- **Reference** holds `billing` and `errors`, which span both planes.

The API reference is **hand-authored MDX**; there is no OpenAPI spec to regenerate from. Endpoint pages use `<ParamField>`/`<ResponseField>` and show curl, Python, and Node examples. If a page is renamed or moved, add a redirect in `docs.json` so old URLs keep working (several exist already).

## Accuracy rules

- The platform is pre-launch and moves fast; the implementation is the source of truth and these docs are hand-written, so drift is the failure mode. **Verify behavior against the live API before documenting it.** Never document from memory or from an example app.
- An API change and its docs change ship together. "Update the docs later" is how the reference rots.
- Pricing, limits, ids, and resource shapes in the docs are load-bearing: they're quoted to customers and parsed by coding agents. Change them only to match a real platform change.

## Image tags in docs

Platform images publish weekly to GHCR (immutable dated tags like `2026.07.02b`, never republished, plus a moving `latest`). The docs must never require a weekly edit to stay correct:

- **Never hardcode a dated tag as guidance.** The two non-rotting sources for "the current version" are the tag in a system template's `image_ref` on `GET /v1/templates`, and the GHCR package pages. Link those instead (`https://github.com/orgs/agent37-platform/packages/container/package/<hermes|hermes-base|hermes-small|openclaw|openclaw-base|claude-code|claude-code-base>`).
- **Dockerfile `FROM` examples use `:latest`**, with the one mental model stated nearby: a built image freezes its base at build time, the FROM tag only matters at rebuild, pin a dated tag for reproducible rebuilds.
- **Runnable request examples use `<tag>` placeholders** (`agent37-hermes@<tag>`, `ghcr.io/you/my-agent:<tag>`). A copy-pasted placeholder fails loudly; a copy-pasted old tag silently pins an ancient release.
- **JSON response examples show a real dated tag for realism; it is illustrative.** An old immutable tag is never wrong, just old: normalize opportunistically when editing a page, never as a release chore. Keep one consistent tag across all response examples when you do touch them.

## Terminology

Use these exactly; consistent terms are what make `llms-full.txt` usable as a spec.

- **Agent37 Cloud** is the product. **Workspace** is the account/billing unit; it owns API keys and one **wallet**.
- **Instance** is an agent's always-on computer at `https://{instanceId}.agent37.app`. **Session** is one conversation on an instance. **Response** is one agentic turn within a session.
- **Agent** vs **model**: the agent is the software running on the instance (Hermes, OpenClaw, Claude Code, and Codex today); the model is the LLM chosen per turn (`model` + `provider`).
- **Template** names the image an instance runs and can set its `default_port`; every other port URL is derivable and needs no declaration. **Gateway** serves the Agent API inside the instance.
- **Instance URL** is an instance's base address, `https://{instanceId}.agent37.app` (the default port, where the agent's API lives). **Preview URL** is a non-default port's address, `https://{instanceId}-{port}.agent37.app`, where a service or UI on that port is reachable. **Signed URL** is a time-boxed, tokenized form of either, mintable via `POST /v1/instances/{id}/signed-url`, that a browser can open without a header.
- Billing: **wallet** (workspace money), **budget** (per-instance managed-spend cap plus **top-up** headroom), **managed services** (LLM / search / Composio through built-in credentials), **micros** (USD × 1,000,000), **past due** (renewal failed; instance force-stopped until a funded start).
- Conventions: instance ids are 10-char lowercase alphanumeric; session/response ids are 32-char hex; Hosting API timestamps are epoch **seconds**, Agent API timestamps epoch **milliseconds**; list endpoints wrap results in `{ "data": [...] }`, newest first.

## Content boundaries

- Document **only the two public planes**. The dashboard, internal/admin APIs, and fleet/host architecture are out of scope.
- The OpenClaw setup guides are **gone from this repo** and are not coming back. All 18 hidden pages (`openclaw/`, `channels/`, `models/`, `runtime/`, `networking/`, `tailscale/`) moved to the blog in July 2026: they were B2C dashboard how-tos making up 22% of `llms-full.txt`, diluting the B2B spec coding agents consume. They live at `www.agent37.com/blog/*` now, and the `website` repo's `vercel.json` redirects every old `/docs/*` URL there. Don't recreate them here, and don't add a redirect for them in `docs.json` (the website handles it).
- The system template catalog has eight entries: `agent37-hermes`, `agent37-hermes-small`, `agent37-openclaw`, `agent37-claude-code`, `agent37-codex`, `agent37-grok`, `agent37-opencode`, and `agent37-n8n`. Claude Code, Codex, Grok, and OpenCode are all live and documented as available. `agent37-n8n` is a web app, not an agent harness: it has no chat API and is served on a default public URL, `https://n8n-{instanceId}.agent37.app`.
- Drafts go in `drafts/` or `*.draft.mdx` (ignored via `.mintignore`).

## Style

- Active voice, second person ("you"); one idea per sentence; sentence case for headings.
- Bold for UI elements: Click **Settings**. Code formatting for file names, commands, paths, endpoints, and field names.
- Lead with the goal; prefer a runnable example over describing one.

### No em dashes

Don't write an em dash (`—`) in docs prose. It is the loudest tell that a page was generated rather
than written, and this reference is the first thing most customers read. Use the punctuation the
sentence actually wants:

| Instead of | Write |
| --- | --- |
| `Existing instances are unaffected — an instance keeps its image.` | A colon, when the second half explains the first: `unaffected: an instance keeps its image.` |
| `The whole folder ships — a stray .env included.` | A comma, for an aside: `The whole folder ships, a stray .env included.` |
| `Every port — dashboard, terminal, files — is routed.` | Parentheses, for a true parenthetical: `Every port (dashboard, terminal, files) is routed.` |
| `Ctrl-C does not cancel the build — it keeps running.` | A semicolon or a full stop, for two independent clauses: `Ctrl-C does not cancel the build; it keeps running.` |

Rewrite the sentence; don't swap the em dash for a hyphen and move on. When you edit a page that
still has one, fix that sentence while you're in there. Sweeping untouched pages is its own change,
not a rider on an unrelated one.

Leave the character alone where it is the content, not the prose: quoted API output, a literal
string a customer copies, a filename or identifier, or a passage that documents the character
itself. En dashes in ranges (`1–30`, `A–Z`) are correct typography and are not what this is about.

Check before you push: `npm run check:dashes`. It prints every hit and exits non-zero if there are
any. It skips this file, the one place that has to spell the character out.
