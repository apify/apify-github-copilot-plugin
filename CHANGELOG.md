# Changelog

All notable changes to the **Apify for GitHub Copilot** plugin are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — Initial GitHub Copilot release

### Added
- `apify` agent (single user-facing entry point for all Apify requests).
- `apify-routing` instruction (soft enforcement of the agent-first pattern).
- `apify` MCP server entry in `apify/.mcp.json` pointing to `https://mcp.apify.com/`.
- Skills: `apify-actor-development`, `apify-actorization`, `apify-generate-output-schema`, `apify-sdk-integration`, `apify-ultimate-scraper`.
- Bundled Node helper scripts for the ultimate-scraper skill (`search_actors.js`, `fetch_actor_details.js`, `run_actor.js`).
- Apache-2.0 license.
