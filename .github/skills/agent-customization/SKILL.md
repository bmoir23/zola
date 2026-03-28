# Agent Skill: Cloudflare Services & AI — agent-customization

Summary
- Purpose: Provide a reusable, workspace-scoped skill that guides agents to set up, configure, develop, and deploy features using Cloudflare services (Workers, Pages, R2, KV, Durable Objects, Images, Stream, Turnstile, CDN and Cloudflare AI/Workers AI/AI Gateway) and interact safely with Cloudflare AI tools.
- Scope: Workspace-scoped (intended to be checked into the repo and used by collaborators). Use `agent-customization` conventions.

When to Use This Skill (Triggers)
- The task mentions Cloudflare, Workers, Pages, R2, KV, Durable Objects, Images, Stream, Turnstile, CDN, or Cloudflare AI/Workers AI/AI Gateway.
- The user asks for deployment, secrets/configuration, API token setup, edge function development, or examples integrating Cloudflare AI features.

Outcomes Produced
- A step-by-step plan for the requested Cloudflare integration (local dev, deploy, test, monitor).
- A minimal runnable example (Workers or Pages) tailored to the repository.
- Recommended environment variables, `wrangler` config, and sample CI step for deployment.
- Security and cost considerations checklist.

High-level Workflow (Step-by-step)
1. Clarify objective and constraints
   - Inputs: feature goal, data residency, expected traffic, cost sensitivity, required SLAs, AI inference type (real-time vs batch).
   - Decision point: prefer Workers function vs Pages + Functions vs full backend.
2. Verify account and permissions
   - Ensure user has Cloudflare account, and create/retrieve API token scoped to required permissions (Workers Scripts, KV, R2, Pages, etc.).
   - Checklist: token stored in CI secrets, local `.env` for dev (not committed).
3. Choose products and architecture
   - Workers for edge compute / low-latency inference; Pages for static + functions; R2 for object storage; KV/Durable Objects for low-latency state.
   - For AI: choose Workers AI or AI Gateway depending on model hosting and latency.
4. Local setup
   - Install `wrangler` CLI and login: `npm install -g wrangler` then `wrangler login`.
   - Create `wrangler.toml` or update existing config with account and bindings.
5. Implement minimal prototype
   - Create a Worker/Function that demonstrates the requested feature (auth, fetch to AI model, cache, error handling).
   - Include environment binding examples: `R2_BUCKET`, `KV_NAMESPACE`, durable object bindings, and secret API keys for AI.
6. Test locally
   - Use `wrangler dev`, and automated unit/integration tests mocking Cloudflare bindings.
7. Deploy
   - Provide `wrangler publish` or Pages CI step; include a sample GitHub Actions job.
8. Post-deploy validation
   - Health checks, sample requests, metrics to track, Rollback plan.
9. Monitoring & cost controls
   - Enable logging, set alert thresholds, and define caching to reduce inference calls.

Decision Points & Branching Logic
- If low-latency inference required -> prefer Workers AI; else consider remote hosted model via AI Gateway.
- If large objects or assets -> use R2 with signed URLs.
- If per-user ephemeral state -> use Durable Objects; for simple key-value -> KV.

Quality Criteria / Completion Checks
- Example runs locally with `wrangler dev` without secrets exposed.
- Deployment config (`wrangler.toml` or `pages` config) present and CI job shows how to publish.
- Secrets and tokens documented and marked for CI secret storage; no secrets committed.
- Basic load and cost guidance provided.

Security & Compliance Notes
- Never store API tokens in repo. Use CI secrets and environment variables.
- Minimize token scopes: create least-privilege API tokens.
- Sanitize and rate-limit user input before passing to any AI model.

Example Commands (copyable)
- Install/CLI:
  - `npm install -g wrangler`
  - `wrangler login`
- Init a worker:
  - `wrangler init my-worker --template "javascript"`
- Publish:
  - `wrangler publish`

Example GitHub Actions snippet (conceptual)
- Provide a CI step that sets `CF_API_TOKEN` as a secret and runs `wrangler publish`.

Files and Changes the Agent May Edit
- `wrangler.toml` (or `wrangler.dev.toml`)
- Worker source under `src/` or `workers/`
- GitHub Actions workflow under `.github/workflows/` to add a deploy job
- Add documentation to repo `README.md` with setup steps

Common Templates to Offer
- Minimal Worker that proxies requests to a Cloudflare AI inference endpoint with caching and error handling.
- Pages + Functions setup for static site + serverless AI endpoints.
- CI job template for automatic deployment on push to `main`.

Ambiguities & Questions to Ask (when invoked)
- Is this workspace-scoped or a personal preference for local-only setup?
- Which Cloudflare features do you want prioritized? (Workers, Pages, R2, KV, Durable Objects, Images, Stream, Turnstile, Workers AI)
- Do you have an existing Cloudflare account and API token? Where do you prefer secrets stored (GitHub Actions, other CI)?
- Do you prefer a full example app or a tiny snippet + integration guide?

Examples of Prompts to Use This Skill
- "Set up a Cloudflare Worker that proxies requests to Workers AI and caches responses for 30s."
- "Create a Pages + Functions example that uses R2 for file uploads and Workers AI for processing."
- "Add a GitHub Actions workflow to publish a Worker on push to main using a `CF_API_TOKEN` secret."

Next Steps After Draft
- Ask clarifying questions listed above to narrow scope.
- Produce the minimal runnable example requested and a CI snippet.

Maintenance & Extensibility
- Keep the skill updated when Cloudflare releases new AI offerings (Workers AI, AI Gateway changes).
- Add concrete code templates per common languages (JS/TS, Rust).

Authorship
- Created by agent-customization skill generator — edit as needed for project conventions.
