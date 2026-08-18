# Apify for GitHub Copilot

Official Apify plugin for GitHub Copilot — adds an `apify` agent, an Apify MCP server connection, a routing instruction, and a set of internal skills for three Apify workflows: using existing Actors from the Apify Store, building or actorizing custom Actors, and integrating Apify into existing applications.

> **Apify** is a platform of thousands of serverless cloud programs called **Actors** for web scraping, browser automation, and data extraction. Learn more at [apify.com](https://apify.com).

## What you get

| Component | Name | Purpose |
|---|---|---|
| Agent (entry point) | `apify` | Routes each Apify request to the right MCP tool, CLI-based flow, or internal skill. **This is the one you should invoke.** |
| MCP server | `apify` (`https://mcp.apify.com/`) | Configured in `apify/.mcp.json`; lets the agent search the Apify Store, fetch Actor details, run Actors, retrieve dataset items, and look up Apify docs when MCP is available. |
| Skill | `apify-actor-development` | Create, debug, test, and deploy a new Apify Actor from scratch. |
| Skill | `apify-actorization` | Convert an existing JavaScript, TypeScript, Python, or CLI-based project into an Apify Actor. |
| Skill | `apify-generate-output-schema` | Analyze an Actor and generate or update `dataset_schema.json`, `output_schema.json`, and `key_value_store_schema.json`. |
| Skill | `apify-sdk-integration` | Add Apify Actor execution to an existing application using the `apify-client` package or REST API. |
| Skill | `apify-ultimate-scraper` | Run Apify Store Actors for multi-step scraping workflows across 15+ platforms using the Apify CLI and bundled workflow references. |

## Installation

Clone (or copy) the plugin contents into your project repository so that the Copilot-specific directories (apify folder with plugin.json and .mcp.json) are in the right places.

```bash
# Clone the plugin repo
git clone https://github.com/apify/apify-github-copilot-plugin /tmp/apify-copilot


```

### Prerequisites

- **VS Code** with the **GitHub Copilot Chat** extension installed and active.
- A GitHub account with Copilot access.

## First-run setup

The plugin supports multiple execution paths behind a single `@apify` entry point. The `apify` agent chooses the right route based on the request, and the required auth depends on that route.

### Path 1 — Using existing Actors (MCP)

For straightforward "search / inspect / run an Actor" tasks, the agent prefers the Apify MCP server when it is available. The first time the agent calls an MCP tool that needs auth (for example `run-actor`), VS Code opens `console.apify.com` in your browser and asks you to sign in. Read-only tools such as `search-actors`, `fetch-actor-details`, `search-apify-docs`, and `fetch-apify-docs` work without auth.

If MCP is not available, the agent can fall back to the Apify CLI for Route 1 tasks, especially for the `apify-ultimate-scraper` skill. In that case, verify the CLI with `apify --help`, then authenticate with `apify login` or use an exported `APIFY_TOKEN` in headless environments.

### Path 2 — Building Actors or integrating via SDK

This path covers two different cases:

- **Actor development / actorization** uses the Apify CLI (`apify create`, `apify init`, `apify run`, `apify push`). In interactive environments, the skills expect `apify login`; in headless environments, they can work with an exported `APIFY_TOKEN`.
- **Application integration** uses the **`apify-client`** package (or REST API) and requires an **`APIFY_TOKEN`** environment variable. Generate one at [console.apify.com/settings/integrations](https://console.apify.com/settings/integrations) and export it before starting VS Code (or add it to your project's `.env`):

```bash
export APIFY_TOKEN="apify_api_xxxxxxxxxxxx"
```

Don't have an account? [Sign up free](https://console.apify.com/sign-up) — no credit card required.

### Working in remote sessions, devcontainers, or SSH (no browser)

The MCP OAuth flow needs a browser. If you're running VS Code over SSH, in a devcontainer, or in any environment where VS Code cannot open a browser, you have two options:

1. **Authenticate locally first.** Connect the Apify MCP server once on your laptop with a normal VS Code session so the OAuth refresh token is stored in your profile, then reconnect remotely.
2. **Use the CLI / SDK paths instead.** The non-MCP flows (`apify-actor-development`, `apify-actorization`, `apify-sdk-integration`, and `apify-ultimate-scraper`) can work without MCP. Use `apify login` where a browser is available, or export `APIFY_TOKEN` for headless sessions.

## How to use it

Open Copilot Chat in VS Code and invoke the `apify` agent. The routing instruction is designed to keep all Apify requests going through that single entry point so the agent can choose the correct transport and skill.

```
@apify find me 5 well-rated coffee shops in Seattle and export to CSV
@apify build me an Actor that scrapes a sitemap and stores titles
@apify add Apify to this Next.js app so I can run a scraper from /api/scrape
@apify generate output schemas for the Actor in this folder
```

## Components reference

### MCP server

The `apify` MCP server is configured in `apify/.mcp.json` and is the preferred Route 1 transport when it is available. The shipped agent expects the server to expose:

- `search-actors` — search the Apify Store by keyword
- `fetch-actor-details` — Actor specs, input schema, and pricing
- `run-actor` — execute an Actor and return run metadata
- `get-dataset-items` — retrieve dataset rows from a previous run
- `search-apify-docs` / `fetch-apify-docs` — Apify documentation lookup

You can disable or re-enable the server in **VS Code Settings → GitHub Copilot → MCP**.

### Bundled scripts

This Copilot package does **not** ship helper scripts under `skills/apify-ultimate-scraper/scripts/`. Instead, the `apify-ultimate-scraper` skill is documented as a CLI-driven workflow and ships markdown references under `skills/apify-ultimate-scraper/references/`, including:

- `actor-index.md` — curated Actor selection guidance
- `gotchas.md` — common pitfalls and warnings
- `workflows/*.md` — playbooks for lead generation, competitive intelligence, brand monitoring, review analysis, SEO/content, and other multi-step use cases

## Troubleshooting

**OAuth browser never opens / hangs.** See the "Working in remote sessions" section above.

**`APIFY_TOKEN not found` or CLI auth failures.** For CLI-based flows (`apify-ultimate-scraper`, `apify-actor-development`, `apify-actorization`), either log in with `apify login` or export `APIFY_TOKEN` before starting the session. For SDK integrations, `apify-client` expects `APIFY_TOKEN` to be available in the environment.

**The wrong skill keeps getting picked.** That's exactly the problem the `apify` routing instruction and agent are designed to prevent — make sure you're starting with `@apify`.

**`apify` vs `apify-client`** — these are two different npm packages. The `apify` package is the SDK for **building** Actors (used inside an Actor's code, on the Apify platform). The `apify-client` package is the API client for **calling** Actors from your own application. The agent picks the right one for you; if you're installing manually, double-check.

**MCP server appears disconnected after VS Code restart.** Open **VS Code Settings → GitHub Copilot → MCP** and toggle the `apify` server off and on. If that doesn't help, re-trigger OAuth by running any Actor command.

## Resources

- Apify Console — [console.apify.com](https://console.apify.com)
- Apify Store — [apify.com/store](https://apify.com/store)
- Apify MCP server — [mcp.apify.com](https://mcp.apify.com/)
- Docs (LLM-friendly) — [docs.apify.com/llms.txt](https://docs.apify.com/llms.txt)
- Docs (full) — [docs.apify.com/llms-full.txt](https://docs.apify.com/llms-full.txt)
- Apify CLI reference — [docs.apify.com/cli](https://docs.apify.com/cli)

## License

Apache-2.0. See [LICENSE](./LICENSE).
