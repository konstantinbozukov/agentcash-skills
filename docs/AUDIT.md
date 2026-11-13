# Repository audit

Date: 2026-11-13. Auditor checkout: `konstantinbozukov/agentcash-skills` at `522d5da81af61e955e228d8dbb0499e0fb85171a` (`master`). No application code, config, or dependencies were changed.

## Summary

This is a working skill catalog, not an application. It teaches agents how to pay for HTTP APIs with the `agentcash` CLI or the hosted AgentCash MCP server (USDC via x402/MPP). The markdown is detailed, the upstream project is published (`Merit-Systems/agentcash-skills`, 23 stars, 23 forks on 2026-11-13), and the documented hosts answered HTTP `HEAD` with `200` (the MCP URL answered `401`). It is not a production service: there is no runtime, no test suite, no CI, and no deploy. This fork was created 2026-09-12 and its `master` SHA matches upstream `master`. It has no commits of its own, issues are disabled, and the README install command still pulls `Merit-Systems/agentcash-skills`.

Maturity: **working**.

## Stack and hosting

| Piece | Evidence |
|---|---|
| Skill markdown | `skills/`, `mcp/skills/`, `plugin/skills/` |
| Claude plugin manifest `0.1.3` | `plugin/.claude-plugin/plugin.json` |
| Codex plugin manifest `0.1.3` | `plugin/.codex-plugin/plugin.json` |
| Hosted MCP URL | `plugin/.mcp.json` → `https://app.agentcash.dev/mcp` |
| Marketplaces | `.claude-plugin/marketplace.json`, `.agents/plugins/marketplace.json` |
| External CLI | npm `agentcash@0.17.1`, published `2026-07-28T00:47:25.467Z` (`npm view`) |
| License file | `plugin/LICENSE` only (Apache-2.0, "Copyright 2025 Merit Systems"). GitHub API `license` for this fork was `null`. |
| App framework, database, IaC | Absent. No `package.json`, Dockerfile, `vercel.json`, Supabase, or AWS files. |

Hosting of this repository is GitHub. Hosting of the paid APIs and the MCP server is outside this repo. On 2026-11-13, `HEAD` returned `200` for `https://agentcash.dev`, `https://app.agentcash.dev`, and the `stable*.dev` origins listed in `skills/agentcash/SKILL.md` (`stablebrowser.dev` redirected to `https://www.stablebrowser.dev/`). `HEAD https://app.agentcash.dev/mcp` returned `401`.

`npx --yes agentcash@0.17.1 --help` also names `stablemerch.dev`, `stablevoice.dev`, `stabletube.dev`, `stablekey.dev`, and `stablememes.dev`. Each returned `200` to `HEAD`. None of them has a skill directory in this repo.

## Deploy path

There is no deploy. `GET /repos/konstantinbozukov/agentcash-skills/actions/workflows` returned `total_count: 0`. There is no `.github/` directory.

What actually ships:

1. Someone publishes markdown to GitHub (`Merit-Systems/agentcash-skills` `master`, currently identical to this fork).
2. An agent runs the README command `npx skills add Merit-Systems/agentcash-skills --all --yes` (CLI mode) or installs the MCP server with `npx agentcash@latest install -y` and then `npx skills add Merit-Systems/agentcash-skills/mcp --all --yes`.
3. Paid calls go to `stable*.dev` through the CLI, or through the already-hosted MCP server at `https://app.agentcash.dev/mcp`.

`plugin/.mcp.json` used to start a local stdio server (`npx agentcash@latest server`). Commit `522d5da` (PR #31, 2026-08-19) replaced that with the hosted HTTP URL. That commit also removed Claude-specific install steps from `plugin/README.md` only. The skill `rules/getting-started.md` files still say Claude Code.

A commit on this fork does not change what `npx skills add Merit-Systems/agentcash-skills` installs.

## Environment variables

Names only. No values were present, and none were printed.

**In this repository:** none. No `.env` file. A scan of the repo found no `KEY=`, `process.env`, or `os.environ` usage. A pattern scan for AWS access keys, private-key blocks, GitHub PATs, Slack tokens, OpenAI-style `sk-` keys, Stripe live keys, and complete JWTs found no hits. Example strings such as `JWT_TOKEN` and a truncated `eyJ…` prefix in skill examples are placeholders, not credentials.

**In the published CLI `agentcash@0.17.1`** (`dist/esm` of the package downloaded to read `--help`; not part of this repo):

- `INVITE_CODE`
- `CLAUDECODE`
- `CI`
- `AGENTCASH_BUILDER_CODE_EXTRA`
- `X402_DEBUG`
- `XDG_CONFIG_HOME`
- `CLAUDE_CONFIG_DIR`
- `CODEX_HOME`
- `AGENTCASH_SEARCH_URL`
- `X402_PRIVATE_KEY`
- `X402_SOLANA_PRIVATE_KEY`

`X402_PRIVATE_KEY` and `X402_SOLANA_PRIVATE_KEY` are secrets. They are not stored in this repo. The CLI also persists a wallet at `~/.agentcash/wallet.json` (stated in `mcp/skills/agentcash-wallet/SKILL.md` line 25 and in `agentcash balance --help`). `balance` and `accounts` were not run, because help says the first use creates that file.

## Test coverage

There are no tests. A filename search found no `*test*` or `*spec*` files, no `pytest.ini`, no Jest or Vitest config, and no Makefile.

Commands run on 2026-11-13:

| Command | Outcome |
|---|---|
| `npm test` | **Failed**, exit `254`. `ENOENT` opening `/workspace/package.json`. Log: `/home/ubuntu/.npm/_logs/2026-11-13T16_56_46_793Z-debug-0.log`. |
| `python3 -m pytest --collect-only` | **Failed** immediately: `/usr/bin/python3: No module named pytest`. Pytest was not installed, because there is nothing to collect. |
| `json.loads` on `plugin/.mcp.json`, `plugin/.claude-plugin/plugin.json`, `plugin/.codex-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `.agents/plugins/marketplace.json` | **Passed.** |
| `npx --yes agentcash@0.17.1 --help` | **Passed.** |
| `npx --yes agentcash@0.17.1 fetch --help` | **Passed.** |
| `npx --yes agentcash@0.17.1 install --help` | **Passed.** |

Not run, on purpose: `npx skills add`, `agentcash install`, `balance`, `accounts`, `discover`, `check`, and any `fetch` of a `stable*.dev` URL. Those write client config, create a wallet, or can spend USDC.

Relative markdown links inside the repo all resolve. That was a file-existence check, not a test suite.

## Bugs and likely bugs

Confirmed by reading the files and, where noted, by CLI 0.17.1 help. Live endpoint schemas were not called, so prices were not re-quoted against the APIs.

1. **Documented prices sit above the CLI's default payment cap.** `agentcash@0.17.1 fetch --help` says `--max-amount` "Aborts if the endpoint requests more. Defaults to $5." No skill example passes `--max-amount`. Prices written in the skills that are above `$5`:
   - `skills/phone-calls/SKILL.md` line 43: buy a number, `$20.00`.
   - `skills/phone-calls/SKILL.md` line 44: top up a number, `$15.00`.
   - `skills/media-generation/SKILL.md` lines 35, 60, and 61: Seedance "up to $20". The sample quote on line 35 (`$0.36` for 4s/480p) is under the cap. The upper bound is not.
   - `skills/upload-and-share/SKILL.md` line 256: site renew "Price = tier price x count ($0.005–$20.00)".
   The same figures are repeated in the MCP copies. A subdomain purchase at `$5.00` (`skills/email/SKILL.md` line 41) sits on the default ceiling. TODO: confirm with Konstantin whether the comparison is `>` or `>=`.

2. **MCP Reddit example is not a URL.** `mcp/skills/social-intelligence/rules/rate-limits.md` line 40 sets `url=".../reddit/search"`. The CLI copy at `skills/social-intelligence/rules/rate-limits.md` line 39 uses the full `https://stableenrich.dev/api/reddit/search`. Lines 48–50 of the MCP file call `agentcash.fetch` with a body and no URL.

3. **One MCP install command is interactive.** Every other MCP `rules/getting-started.md` uses `npx agentcash@latest install --client claude-code -y`. `mcp/skills/web-research/rules/getting-started.md` line 7 omits `-y`. CLI help describes `-y` as "Yes to all prompts".

4. **Install instructions target Claude Code.** `plugin/skills/agentcash/rules/getting-started.md` line 9 and the twelve MCP `rules/getting-started.md` files pass `--client claude-code`. `install --help` on 0.17.1 lists `cursor` among the choices, and the top-level help example is `agentcash install --client cursor`. Commit `db66496` (2026-08-19) removed client-specific steps from `plugin/README.md` only. `README.md` lines 24–25 show `npx agentcash@latest install -y` with no `--client`. TODO: confirm with Konstantin what that form does when several clients are installed. It was not executed.

5. **Typo in the MCP wallet skill.** `mcp/skills/agentcash-wallet/SKILL.md` line 55: "the Available Services table belowfirst." The plugin copy at `plugin/skills/agentcash/SKILL.md` line 47 has the space.

6. **Three "core" skills have drifted.** All three frontmatters say versioned AgentCash access and mention Tempo. The bodies do not match:
   - `skills/agentcash/SKILL.md` `metadata.version` `3.1` (CLI). Service table lines 107–108 include "programmatic mailboxes" and "iMessage/FaceTime lookup ($0.05)".
   - `plugin/skills/agentcash/SKILL.md` `metadata.version` `3.1` (plugin). Lines 105–106 omit both of those clauses. Line 17 says "x402 or MPP". Line 41 says "Only Base or Solana network USDC. Other networks or tokens will be lost."
   - `mcp/skills/agentcash-wallet/SKILL.md` `metadata.version` `2.3`. Line 17 says "x402-protected" only. Line 49 repeats the Base-or-Solana warning. The service table lines 115–116 still include mailboxes and iMessage lookup.
   CLI `fetch --help` lists `--payment-network` choices `base`, `solana`, and `tempo`. The "other networks will be lost" sentence conflicts with that flag. TODO: confirm with Konstantin whether a Tempo USDC transfer is a supported deposit or a loss. `accounts` was not run.

7. **README catalog is behind the tree.**
   - Line 16: "What can I do with with agentcash?"
   - Line 42: social scraping "across 6 platforms". `skills/social-scraping/SKILL.md` line 33 says "20+ platforms" and line 150 says "~155 endpoints".
   - Line 51: "Both directories contain the same skills". `skills/` has `agentcash`, `agentcash-onboarding`, and `enrich-knowledge-graph`. `mcp/skills/` has `agentcash-wallet` instead. `agentcash`, `agentcash-onboarding`, and `enrich-knowledge-graph` have no `rules/` directory. The README says each skill has one.

8. **`enrich-knowledge-graph` shells out to macOS `open`.** `skills/enrich-knowledge-graph/SKILL.md` line 50: `open https://agentcash.dev/onboard`. The same file calls `npx agentcash` without `@latest` (lines 42, 88, 110, 122, 158, 175, 181). Other skills use `npx agentcash@latest`. Long flags `--method` and `--body` are valid on CLI 0.17.1, so those flags are not a bug. The skill is absent from the README table.

9. **CLI getting-started installs a global binary it does not use.** Example: `skills/data-enrichment/rules/getting-started.md` lines 7 and 12. Step 1 is `npm install -g agentcash`. Step 2 is `npx agentcash@latest balance`. The same pattern is in every CLI `rules/getting-started.md`.

10. **Upload funding link exists only on the CLI copy.** `skills/upload-and-share/rules/getting-started.md` line 18 links `https://x402scan.com/mcp/deposit/<your-wallet-address>`. The MCP copy of that file does not.

Shared quick-reference prices between `skills/<name>` and `mcp/skills/<name>` matched for the domain skills that exist in both trees. The drift is invocation style, the items above, and the three core skills.

## Security risks

No committed secret was found. Paths that would have been reported by value are not listed because the scan did not match. Placeholder tokens in examples are not secrets.

Risks that are real:

- **Wallet key on disk.** The CLI creates `~/.agentcash/wallet.json`. The npm package also honors `X402_PRIVATE_KEY` and `X402_SOLANA_PRIVATE_KEY`. Either one is a spendable key. This repo's `.gitignore` does not mention `.env`, `wallet.json`, or those variable names. The documented path is under the home directory, so a normal CLI run does not drop the key into the git tree. Do not copy it into the repo.
- **Skills spend money when followed.** Phone numbers are `$20`, inboxes are `$1`, subdomains are `$5`, StableSocial triggers are `$0.06`. Failed HTTP calls are documented as unbilled. An agent that skips `check` can still pay a successful call the user did not intend.
- **People and property data.** `skills/people-property/SKILL.md` lines 17–18 and `rules/privacy.md` warn about Whitepages data (addresses, phones, relatives) and FCRA limits. The skill still gives the exact paid endpoints.
- **Calls record by default.** `skills/phone-calls/SKILL.md` line 68: `record` defaults to `true`. US/CA `+1` only (line 59). DNC hits return 403 (line 75).
- **Job tokens in query strings.** `skills/phone-calls/SKILL.md` line 47 and `skills/web-research/SKILL.md` document `?token=` poll URLs. `skills/social-scraping/rules/getting-started.md` line 28 says the legacy token TTL is 30 minutes. Tokens in URLs leak via history and logs.
- **Vercel Blob client tokens.** `skills/media-generation/rules/uploads.md` lines 12–17 tell the agent to take a `clientToken` from the paid upload call and send it as `authorization: Bearer` to `https://vercel.com/api/blob/`. That token is a runtime credential. It is not committed.
- **Hosted MCP has no credential in git.** `plugin/.mcp.json` is only a URL. Unauthenticated `HEAD` returned `401`, which is the expected gate. The marketplace policy is `"authentication": "ON_INSTALL"` (`.agents/plugins/marketplace.json`).
- **Fork GitHub settings.** Secret scanning and push protection are enabled. Dependabot security updates are disabled. That setting is unused while the repo has no dependency manifest.

## Dependency and staleness

This repo has no dependency manifest, lockfile, or Renovate/Dependabot config. Nothing here can be `npm audit`ed.

Staleness that was measured:

| Item | Date | Note |
|---|---|---|
| Last commit on `master` | 2026-08-19 | `522d5da`, plugin marketplace. Same SHA on `Merit-Systems/agentcash-skills` `master`. |
| Last "sync with live APIs" commit | 2026-08-11 | `3d657b7`. |
| `agentcash` npm latest | 2026-07-28 | `0.17.1`. About two weeks older than the skill sync. |
| This audit | 2026-11-13 | About 86 days after the last commit. |
| Fork created | 2026-09-12 | `pushed_at` on the GitHub repo object was 2026-09-13. No unique commits. |

Consequences:

- The CLI help text shipped in `0.17.1` still advertises merch, voice, tube, key, and memes, and its short origin list omits phone, jobs, travel, and browser. The August skill sync did the reverse. Agents can miss a live origin or follow a skill the installed CLI blurb does not mention.
- `metadata.version` inside skills (`2` through `3.2`), plugin `0.1.3`, and npm `0.17.1` are three unrelated sequences.
- Prices were last edited in-repo on 2026-08-11. They were not re-quoted against the live APIs in this audit.
- `npm install -g agentcash` and `npx agentcash@latest` are both unpinned. A later npm publish can change payment behavior without a commit here. The `$5` default is an example already in `0.17.1`.

## Top 5 improvements

Ranked by value divided by effort. These are recommendations. This audit did not implement them.

1. **Teach the `$5` cap and the real client. Value: high. Effort: small.** Add `--max-amount` to every example whose table price can exceed `$5` (phone number, number top-up, Seedance upper bound, site renew). Replace `--client claude-code` with the client actually in use (`cursor` in Cursor). Add the missing `-y` on `mcp/skills/web-research/rules/getting-started.md` line 7. Fix `belowfirst` (`mcp/skills/agentcash-wallet/SKILL.md` line 55) and the README typo (`README.md` line 16). Agents hit these on the first expensive call.

2. **One source for CLI and MCP skills. Value: high. Effort: medium.** Eleven domain skills are hand-copied twice, and three core skills (`skills/agentcash`, `plugin/skills/agentcash`, `mcp/skills/agentcash-wallet`) have already diverged on version, MPP wording, mailboxes, and iMessage. Generate the second tree, or fail CI when endpoint sets or prices differ. The Reddit placeholder URL is the kind of drift a diff would catch.

3. **Re-sync origins against `agentcash@latest` and the live `discover` output. Value: high. Effort: medium.** Add skills or README rows for `stablemerch.dev`, `stablevoice.dev`, `stabletube.dev`, `stablekey.dev`, and `stablememes.dev` if they are still products. Re-check phone, jobs, travel, and browser, which the July 28 CLI blurb omitted. Re-quote prices. Do this only after confirming `discover` is free. TODO: confirm with Konstantin.

4. **Add a no-network consistency check. Value: medium. Effort: small.** A script can parse frontmatter, resolve relative links, diff CLI vs MCP URL and price sets, and reject `--client claude-code` outside an explicit Claude section. That is the test suite this repo can have without spending USDC. Wire it to GitHub Actions on the repo that is actually the install source.

5. **Decide what this fork is, and make the license and catalog match. Value: medium. Effort: small.** If the fork stays a mirror, say so in the README and keep installing from `Merit-Systems/agentcash-skills`. If it is Konstantin's copy, point `npx skills add` at `konstantinbozukov/agentcash-skills` and expect to merge upstream on purpose. Put Apache-2.0 at the repo root if the whole tree is under that grant (today only `plugin/LICENSE` exists, and GitHub reports no license). Add `enrich-knowledge-graph` and `agentcash-onboarding` to the README table, and correct "6 platforms" (`README.md` line 42) to match `skills/social-scraping/SKILL.md` line 33.

## Open questions for Konstantin

- Is this fork a mirror of Merit Systems, or the tree agents should install?
- Should Cursor sessions install with `--client cursor`, and what does `npx agentcash@latest install -y` do with no `--client`?
- Is Tempo a deposit network? Plugin and wallet skills say any network other than Base or Solana loses the funds (`plugin/skills/agentcash/SKILL.md` line 41). CLI 0.17.1 accepts `--payment-network tempo` and has a `bridge` command the skills never mention.
- Is the `--max-amount` default still `$5` on the CLI you want agents to run? Should skills raise it for `$15` and `$20` calls?
- Are the 2026-08-11 prices still the live quotes? This audit did not call `check` or `discover`.
- Should `stablemerch.dev`, `stablevoice.dev`, `stabletube.dev`, `stablekey.dev`, and `stablememes.dev` get skills? They responded to `HEAD` and are in the CLI help, and they are absent here.
- Is default call recording (`skills/phone-calls/SKILL.md` line 68) what you want agents to do?
- Who should own updates if Merit Systems publishes again? Upstream `pushed_at` was still 2026-08-19 when checked. Upstream `open_issues_count` was 39, which GitHub uses for issues and pull requests combined. Those items were not triaged.
