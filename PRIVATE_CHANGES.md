# Private Changes Ledger

This file tracks active local-only code changes in this checkout that are not part of upstream `openclaw/openclaw`.

Use it for:

- local hotfixes written directly in this repo
- manual ports or cherry-picks of upstream PRs that are not merged yet
- fork-only behavior changes that must survive future upstream upgrades

Do not use it for:

- config changes outside the repo
- upstream changes that have already merged and been adopted here
- experiments that were reverted before handoff

Upgrade rule:

- When rebasing onto upstream or merging an official upstream tag, review every active entry here.
- If upstream now includes an equivalent fix, prefer the upstream implementation and delete the entry.
- If the local change still differs intentionally, keep the entry and refresh its notes.
- This file should contain only active private changes.

## Active Entries

### 2026-03-23 - Diagnostic status probes stay lightweight and lazy-load gateway validators

- Kind: `local fix`
- Status: `active`
- Summary: Reduced the work done by CLI/probe diagnostic clients so `openclaw status` style checks stay responsive, and changed gateway protocol validators plus doctor-config loading to lazy initialization instead of eagerly compiling/loading heavy modules up front.
- Why local: The live system was showing sluggish diagnostic behavior, and this checkout now carries a local performance/latency fix that is not yet tracked as an adopted upstream release change.
- Upstream reference: `none`
- Local commits: `64c9a576ba`
- Local touchpoints: `src/cli/program/config-guard.ts`, `src/cli/route.ts`, `src/commands/status.scan.test.ts`, `src/commands/status.scan.ts`, `src/gateway/client.ts`, `src/gateway/probe.test.ts`, `src/gateway/probe.ts`, `src/gateway/protocol/index.ts`, `src/gateway/server/ws-connection/message-handler.ts`, `src/utils/message-channel.test.ts`, `src/utils/message-channel.ts`
- Upgrade review: On each official tag upgrade, check whether upstream now lazy-loads gateway protocol validators and keeps diagnostic status/probe clients from triggering extra presence/health work or broad RPC fan-out. If the chosen tag contains equivalent behavior, prefer the official implementation and retire this entry.
- Retirement plan: `drop during merge`
- Remove when: The chosen upstream tag contains equivalent lazy validator loading and lightweight diagnostic probe behavior, and this local patch has been removed in favor of the official implementation.

### 2026-03-07 - Fake-IP SSRF allowCidrs support for web_fetch

- Kind: `ported upstream PR`
- Status: `active`
- Summary: Ported upstream PR #28657 into the fork so `web_fetch` can opt in to `tools.web.fetch.ssrfPolicy.allowCidrs`, which is needed for OpenClash fake-ip environments that resolve through `198.18.0.0/15`.
- Why local: The live environment needed fake-ip support immediately. Local memory and git history show Jamie integrated PR #28657 into the fork, and the resulting local commit chain is still present on `main` while not being an ancestor of `upstream/main`.
- Upstream reference: https://github.com/openclaw/openclaw/pull/28657
- Local commits: `da2ea9860c`
- Local touchpoints: `src/agents/tools/web-fetch.ts`, `src/config/schema.help.ts`, `src/config/schema.labels.ts`, `src/config/types.tools.ts`, `src/config/zod-schema.agent-runtime.ts`, `src/infra/net/fetch-guard.ssrf.test.ts`, `src/infra/net/ssrf.pinning.test.ts`, `src/infra/net/ssrf.test.ts`, `src/infra/net/ssrf.ts`, `src/shared/net/ip.ts`
- Upgrade review: On each official tag upgrade, confirm on upstream GitHub whether PR #28657 or an equivalent implementation has merged, then confirm the chosen tag actually contains that fix. If yes, compare behavior and tests, keep the official implementation, and retain the local runtime config exception `tools.web.fetch.ssrfPolicy.allowCidrs: [\"198.18.0.0/15\"]` only if it is still needed operationally.
- Retirement plan: `revert after merge`
- Remove when: The chosen upstream tag contains equivalent `allowCidrs` support for `web_fetch`, and local commit `da2ea9860c` has been reverted or otherwise made redundant in favor of the official implementation.

### 2026-03-15 - Control UI context warning uses accumulated input tokens

- Kind: `ported upstream PR`
- Status: `active`
- Summary: The Control UI context warning used accumulated `inputTokens` instead of fresh `totalTokens`, which overstated actual context usage and could produce bogus `100% context used` warnings after long runs or compaction.
- Why local: The bug is confirmed in this checkout, and this repo now carries a local UI fix so the warning only uses fresh token snapshots.
- Upstream reference: https://github.com/openclaw/openclaw/issues/45230, https://github.com/openclaw/openclaw/issues/45513, https://github.com/openclaw/openclaw/pull/45335, https://github.com/openclaw/openclaw/pull/45648
- Local commits: `142ce81b1a`
- Local touchpoints: `ui/src/ui/types.ts`, `ui/src/ui/views/chat.ts`, `ui/src/ui/views/chat.test.ts`
- Upgrade review: On each official tag upgrade, confirm whether the target upstream tag already uses fresh `totalTokens` / `totalTokensFresh` semantics for the chat context warning. If it does, prefer the official implementation and drop this local patch rather than carrying two competing versions of the notice logic.
- Retirement plan: `drop during merge`
- Remove when: The chosen upstream tag contains the correct context-warning logic and this local patch has been removed in favor of the official implementation.

## Entry Template

### YYYY-MM-DD - Short title

- Kind: `local fix` | `local feature` | `ported upstream PR` | `fork-only behavior`
- Status: `active`
- Summary: what problem this change solves
- Why local: why this is not just normal upstream code yet
- Upstream reference: `none` or full URL(s)
- Local commits: commit SHA(s), `uncommitted`, or `unknown`
- Local touchpoints: `path/to/file.ts`, `another/path.ts`
- Upgrade review: what to check when syncing to an official upstream tag
- Retirement plan: `drop during merge` | `revert after merge` | `keep local delta`
- Remove when: the condition that makes this entry obsolete
