# AGENTS.md

Rules for Cursor agents working in this repository. This is a documentation and plugin pack. It has no application runtime of its own.

Audit date: 2026-11-13. Facts below were checked against commit `522d5da81af61e955e228d8dbb0499e0fb85171a` on `master`.

## What this project is

`agentcash-skills` is a catalog of Agent Skills for calling paid and SIWX (sign-in-with-X) APIs through the `agentcash` CLI or the hosted AgentCash MCP server. Calls are paid in USDC per request (x402 / MPP). There is no API key in this repo.

This GitHub repo is `konstantinbozukov/agentcash-skills`. It is a fork of `Merit-Systems/agentcash-skills`. On 2026-11-13, `master` here and upstream `master` were the same commit: `522d5da` (2026-08-19, "Merge pull request #31"). This fork has no commits of its own.

The published install command in `README.md` still adds skills from `Merit-Systems/agentcash-skills`, not from this fork. A change committed only here will not reach agents who follow that README until the install source is changed. TODO: confirm with Konstantin whether this fork is meant to stay a mirror or to diverge.

## Stack

- Content: Markdown skills (`SKILL.md` plus optional `rules/*.md`) with YAML frontmatter.
- Plugin manifest: JSON for Claude (`plugin/.claude-plugin/plugin.json`, version `0.1.3`) and Codex (`plugin/.codex-plugin/plugin.json`, version `0.1.3`).
- MCP server declaration: `plugin/.mcp.json` points at the hosted HTTP endpoint `https://app.agentcash.dev/mcp`.
- Marketplaces: `.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json`.
- External CLI, not vendored here: npm package `agentcash`. On 2026-11-13, `npm view agentcash version` reported `0.17.1`, published `2026-07-28T00:47:25.467Z`.
- No `package.json`, lockfile, Dockerfile, `vercel.json`, Supabase config, AWS config, or GitHub Actions workflow in this repo.

## Folder layout

```
README.md                         CLI and MCP install instructions and the skill index
skills/                           CLI-mode skills (shell examples, npx agentcash)
  agentcash/                      Core wallet + discover + fetch skill (no rules/ directory)
  agentcash-onboarding/           One-time setup skill (no rules/ directory)
  enrich-knowledge-graph/         Wiki/people enrichment workflow (no rules/ directory)
  data-enrichment/                People, company, email verification
  email/                          StableEmail
  local-search/                   Google Maps, solar, aerial view
  media-generation/               StableStudio image and video
  news-shopping/                  Serper news and shopping
  people-property/                Whitepages people and property
  phone-calls/                    StablePhone
  social-intelligence/            Reddit via stableenrich
  social-scraping/                StableSocial
  upload-and-share/               StableUpload
  web-research/                   Exa, Firecrawl, Cloudflare crawl
mcp/skills/                       MCP-mode copies (agentcash.fetch examples)
  agentcash-wallet/               MCP core skill. Name and version differ from skills/agentcash
  …                               Same domain skills as skills/, invoked via MCP
plugin/                           Plugin bundle (one skill, MCP URL, logos, Apache-2.0 LICENSE)
  skills/agentcash/               MCP-oriented core skill, metadata.version 3.1
  .mcp.json                       Hosted MCP URL
  .claude-plugin/  .codex-plugin/ Client manifests
.agents/plugins/marketplace.json  Local marketplace entry pointing at ./plugin
.claude-plugin/marketplace.json   Claude marketplace entry
```

`README.md` says the `skills/` and `mcp/skills/` trees contain the same skills. They do not. CLI-only directories: `agentcash`, `agentcash-onboarding`, `enrich-knowledge-graph`. MCP-only directory: `agentcash-wallet`. Shared skills differ by invocation style (shell vs `agentcash.fetch`). Prices in the shared quick-reference tables matched on 2026-11-13.

`README.md` also omits `enrich-knowledge-graph` and `agentcash-onboarding`.

## Install, run, test, lint, build

There is nothing in this repo to install, lint, or build. These commands were actually run on 2026-11-13:

| Command | Result |
|---|---|
| `npm test` | Exit 254. npm could not read `/workspace/package.json` (`ENOENT`). |
| `python3 -m pytest --collect-only` | Failed immediately: `No module named pytest`. No test files exist. Pytest was not installed. |
| `python3` `json.loads` on every `*.json` file | Passed for all five JSON files. |
| `npx --yes agentcash@0.17.1 --help` | Passed. Prints the CLI command list. |
| `npx --yes agentcash@0.17.1 fetch --help` | Passed. Confirms `-m`/`--method`, `-b`/`--body`, and `--max-amount` default `$5`. |
| `npx --yes agentcash@0.17.1 install --help` | Passed. `--client` choices include `cursor`, `claude-code`, `codex`, and others. |

Do not run these during a docs-only task. They spend money or create a wallet:

- `npx agentcash@latest balance` and `accounts` — CLI help says both create `~/.agentcash/wallet.json` on first use.
- `npx agentcash@latest fetch` against any `stable*.dev` URL — paid or authenticated call.
- `npx agentcash@latest install` — writes MCP config into a client.
- `npx skills add …` — copies skills into the agent environment.

Documented install commands (from `README.md`, not executed here):

```bash
npx skills add Merit-Systems/agentcash-skills --all --yes
npx agentcash@latest install -y
npx skills add Merit-Systems/agentcash-skills/mcp --all --yes
```

CLI skill `rules/getting-started.md` files instead say `npm install -g agentcash`, then call `npx agentcash@latest`. The global install is not what the next command runs. MCP `rules/getting-started.md` files hardcode `npx agentcash@latest install --client claude-code`. `install --help` on 0.17.1 also accepts `--client cursor`. For work in Cursor, pass `--client cursor`. TODO: confirm with Konstantin what `install -y` does when `--client` is omitted. That command was not run, because it writes client config.

Day-to-day read-only checks an agent can run:

```bash
npx --yes agentcash@0.17.1 fetch --help
npx --yes agentcash@0.17.1 discover --help
```

`discover` and `check` were not run against live origins in the 2026-11-13 audit. Treat them as network calls that might be billed. TODO: confirm with Konstantin that `discover` and `check` are free before running them.

## Deploy

This repo has no deploy pipeline. No `.github/workflows`, no Vercel project, no build step.

Distribution is the git repository itself:

- Agents install skills with `npx skills add Merit-Systems/agentcash-skills`.
- The plugin's MCP server is already hosted. `plugin/.mcp.json` sets `url` to `https://app.agentcash.dev/mcp`. A `HEAD` request on 2026-11-13 returned `401`.
- A `HEAD` request to each of these returned `200` on 2026-11-13: `agentcash.dev`, `app.agentcash.dev`, `stableenrich.dev`, `stablestudio.dev`, `stablesocial.dev`, `stableupload.dev`, `stableemail.dev`, `stablephone.dev`, `stablejobs.dev`, `stabletravel.dev`, `stablebrowser.dev` (redirected to `www.stablebrowser.dev`).

Do not change `plugin/.mcp.json`, the marketplace JSON, or the plugin manifests unless Konstantin asks for a plugin release.

## External services

Paid origins documented in `skills/agentcash/SKILL.md`:

| Origin | Role |
|---|---|
| `stableenrich.dev` | People, company, web search, scraping, Maps, news, Whitepages, Reddit, email verify |
| `stablesocial.dev` | Social scraping and Lightreel |
| `stablestudio.dev` | Image and video generation |
| `stableupload.dev` | File and static-site hosting |
| `stableemail.dev` | Email send, inboxes, subdomains |
| `stablephone.dev` | AI calls and phone numbers |
| `stablejobs.dev` | Job search |
| `stabletravel.dev` | Flights |
| `stablebrowser.dev` | Cloud browser automation |

`npx --yes agentcash@0.17.1 --help` also lists origins that have no skill directory here: `stablemerch.dev`, `stablevoice.dev`, `stabletube.dev`, `stablekey.dev`, `stablememes.dev`. Each returned HTTP `200` to `HEAD` on 2026-11-13. The same help text's baked-in origin list does not mention `stablephone.dev`, `stablejobs.dev`, `stabletravel.dev`, or `stablebrowser.dev`, which this repo does document. The npm package was published 2026-07-28. The skill sync commit is 2026-08-11 (`3d657b7`). The two lists have drifted.

Other services named in the docs:

- Hosted MCP and account site: `https://app.agentcash.dev` and `https://app.agentcash.dev/mcp`.
- Marketing site: `https://agentcash.dev`.
- Vercel Blob: upload step in `skills/media-generation/rules/uploads.md` (`PUT https://vercel.com/api/blob/`). This repo is not a Vercel app.
- Deposit UI linked only from `skills/upload-and-share/rules/getting-started.md`: `https://x402scan.com/mcp/deposit/<your-wallet-address>`.
- Privacy and terms links in `plugin/.codex-plugin/plugin.json`: `https://merit.systems/privacy` and `https://merit.systems/terms`.

No Supabase, AWS, or database client appears in the repo.

Wallet file, created by the CLI outside the repo: `~/.agentcash/wallet.json` (`mcp/skills/agentcash-wallet/SKILL.md` and `agentcash@0.17.1` help).

## Environment variables

This repository contains no environment variables and no `.env` files. A search for assignment lines and `process.env` in the repo found nothing.

The published CLI `agentcash@0.17.1` (installed only to read `--help`, then inspected under `dist/esm`) reads these names. None are set or committed here. Do not print or invent values.

| Name | Where it was seen |
|---|---|
| `INVITE_CODE` | `dist/esm/server-*.js` |
| `CLAUDECODE` | `dist/esm/index.js` |
| `CI` | `dist/esm/index.js` |
| `AGENTCASH_BUILDER_CODE_EXTRA` | `dist/esm/chunk-YIECO2O4.js` |
| `X402_DEBUG` | `dist/esm/chunk-ITCDZXBZ.js` |
| `XDG_CONFIG_HOME` | `dist/esm/chunk-FVVSNDQR.js` |
| `CLAUDE_CONFIG_DIR` | `dist/esm/chunk-FVVSNDQR.js` |
| `CODEX_HOME` | `dist/esm/chunk-FVVSNDQR.js` |
| `AGENTCASH_SEARCH_URL` | `dist/esm/chunk-G6MIWS35.js` |
| `X402_PRIVATE_KEY` | `dist/esm/chunk-F3KGAMIA.js` |
| `X402_SOLANA_PRIVATE_KEY` | `dist/esm/chunk-F3KGAMIA.js` |

`X402_PRIVATE_KEY` and `X402_SOLANA_PRIVATE_KEY` are wallet secrets. Never commit them. Never echo them.

## Coding conventions

Inferred from the files, not from a formatter config (there is none).

- Each skill is a directory with `SKILL.md`. Optional guidance lives in `rules/`.
- Frontmatter fields in use: `name`, `description`, `homepage` (some skills), `metadata.version`, and on `enrich-knowledge-graph` also `argument-hint`.
- `description` is a routing prompt: `USE FOR`, `TRIGGERS`, and often "use the exact paths from the Quick Reference table".
- `metadata.version` is per skill (`2`, `2.2`, `2.3`, `3.1`, `3.2`). It is not the plugin version (`0.1.3`) and not the npm version (`0.17.1`).
- CLI skills show `bash` fences and `npx agentcash@latest fetch <url> -m POST -b '{...}'`. Long flags `--method` and `--body` also exist on CLI 0.17.1. `enrich-knowledge-graph` uses the long flags and drops `@latest`.
- MCP skills show `mcp` fences and `agentcash.fetch(url=..., method="POST", body={...})`.
- Endpoint paths include a provider prefix (`/api/fullenrich/people-search`). The README forbids guessed paths.
- Prices belong in the Quick Reference table and are repeated in `rules/getting-started.md`. Change both copies, and change both `skills/` and `mcp/skills/`, in the same edit.
- Keep CLI and MCP wording aligned. A typo already slipped through: `mcp/skills/agentcash-wallet/SKILL.md` line 55 says `belowfirst`.
- Plugin code and marketplace JSON are release config. Skill markdown is the product.

## Never touch

- Secrets and wallet material: `~/.agentcash/wallet.json`, `X402_PRIVATE_KEY`, `X402_SOLANA_PRIVATE_KEY`, invite codes, payment signatures, Vercel Blob `clientToken` values returned at runtime.
- `.env` files. None are committed. Do not add one with real values.
- Production payment data and live calls. Do not `fetch` a paid URL, redeem a code, or buy a number, inbox, or subdomain unless Konstantin explicitly asks for that call.
- Migrations. There is no database and no migration directory. Do not invent one.
- Deploy and store config: `plugin/.mcp.json`, `plugin/.claude-plugin/plugin.json`, `plugin/.codex-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `.agents/plugins/marketplace.json`.
- `plugin/LICENSE`. The Apache-2.0 grant is the only license file. There is no root `LICENSE`. GitHub's API reported `license: null` for this fork on 2026-11-13.

## Safe edit checklist

1. If you change an endpoint or a price, update the CLI skill, the MCP skill, and that skill's `rules/getting-started.md` together.
2. If a documented price can exceed `$5`, include `--max-amount` on the CLI example. CLI 0.17.1 aborts when the endpoint asks for more than the default `$5`. Affected docs today include phone-number purchase at `$20` (`skills/phone-calls/SKILL.md` line 43), number top-up at `$15` (line 44), and Seedance video "up to `$20`" (`skills/media-generation/SKILL.md` lines 35 and 60).
3. Do not hardcode `--client claude-code` for a Cursor session.
4. Do not guess a new endpoint path. Point at `discover` / `check` instead.
5. Leave `README.md` install URLs on `Merit-Systems/agentcash-skills` unless Konstantin says this fork is the install source.
