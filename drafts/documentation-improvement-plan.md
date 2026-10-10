# Agent37 documentation improvement plan

October 9, 2026. Original audit and implementation plan, updated with the sandbox-first direction below. The evidence table describes the docs before this change.

**The main problem is content order and navigation.** Put a successful request first, then explain the options. Humans and coding agents both benefit from small, complete pages organized around a specific task or operation.

## What feels wrong today

| Evidence | Effect |
| --- | --- |
| [Instances](../agents-api/instances.mdx) puts 11 parameter fields and a pricing table before its first runnable request. | Creating an instance looks much harder than it is. |
| The same 618-line page covers 14 API operations. | Individual operations are hard to find, link to, and retrieve. |
| [Send a message](../agents-api/chat.mdx) puts request and response schemas before its first request example. | Readers must understand the whole contract before trying it. |
| [Navigation](../docs.json) places 26 build-guide pages before the APIs. | Common API tasks are buried below a long sidebar. |
| [Quickstart](../index.mdx) jumps from creation to chat; Instances separately says to wait for agent health. | The first-run path needs its readiness guidance reconciled. |
| The live [full Markdown export](https://www.agent37.com/docs/llms-full.txt) is about 640,000 characters. | Agents benefit from an index and focused page retrieval instead of always loading everything. |

## Changes, in order

1. **Pilot two pages: Create an instance and Send a message.** Start with one sentence, method and full URL, the correct authentication header, a runnable request, and a short, clearly labeled response excerpt. Keep prerequisites visible, including funding, managed-service budget, and agent readiness where relevant. Follow with required and optional parameters, complete response fields, errors, and advanced behavior. Retain curl, Python, and Node examples.

2. **Split Instances by operation.** Give create, list, get, edit, delete, lifecycle actions, fork, and backup operations their own pages. Keep a short Instances overview and a canonical instance-object reference. Move extended explanations of sizing, environment variables, and auto-sleep into linked guides. Preserve existing URLs and section links through compatibility sections or redirects where appropriate; redirects alone do not preserve renamed fragments.

3. **Separate Guides and API reference with top-level tabs.** Within API reference, keep Hosting API, Agent API, and shared reference distinct. Within Guides, lead with Quickstart and concepts, then group use cases, harnesses, and integrations. Readers should reach Create an instance within two navigation clicks.

4. **Make Quickstart sandbox-first.** Set the key once, create an instance, run a command, reach a service through its preview URL, and explain cleanup. Explain that a template selects the software; chat is an optional capability of agent templates. Keep the agent walkthrough separate, with an explicit managed-service budget and a bounded readiness poll before the first message. Verify both paths against the live API before publishing.

5. **Use Mintlify's API layout.** Prototype its [manual endpoint pages and request/response panels](https://www.mintlify.com/docs/api-playground/mdx-setup) without requiring an OpenAPI migration. Check the two hosts and authentication schemes separately. Inspect desktop at 100% zoom and mobile before changing typography; the screenshots appear zoomed out.

6. **Keep one accurate source for humans and agents.** Preserve complete types, defaults, nullability, units, limits, error semantics, and harness-specific exceptions. Every endpoint must identify its host and authentication: Hosting uses `Authorization: Bearer`; Agent uses `X-Agent37-Key`. Check that page Markdown and `llms-full.txt` retain all essential content after layout changes. Keep `llms.txt` useful for targeted retrieval and preserve `llms-full.txt` for existing clients.

## Benchmarks and completion checks

Use [E2B](https://docs.e2b.dev/) for its early runnable example and [Browserbase](https://docs.browserbase.com/welcome/introduction) for task-based entry points. Keep Agent37's persistent-instance model and terminology.

Review the two-page pilot before expanding it. A new developer should find and copy a request without scrolling through schemas. A fresh coding agent using only public Markdown should create an instance, run a command, and reach its service with the correct headers. The optional agent path should wait for readiness before sending a message. Validate examples in a test workspace against the deployed API, check old links and fragments, run Mintlify validation and broken-link checks, and compare exported contracts for lost fields or constraints.

Implementation cross-check: [create-instance handler](../../agent37-web/app/api/helpers/agentInstances.ts) accepts an empty body and defaults omitted budget fields to zero. Distinguish the smallest valid create request from the setup needed for a successful managed-model conversation.
