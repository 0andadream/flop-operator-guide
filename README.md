<div align="center">

# FLOP Operator Guide

**Testnet validator + GPU miner + agent setup — this network is where work starts to count.**

<a href="https://x.com/mattdreams"><img src="assets/banner.png" width="100%" alt="FLOP Operator Guide - Deployment Guide by Matt" /></a>

</div>

---

> **⚠️ PLEASE TAKE NOTE**
>
> **FLOP Testnet is live as of 24 November 2026**, but this is a **very early release**. [@flop_labs](https://x.com/flop_labs/status/2125212060481093834) and the [operator hub](https://docs.flop.network/operators?network=testnet) say features are being activated over the coming hours. The official explorer and operator hub currently describe the network as **unscored**: **voucher earning and trading are disabled**, and **prior activity earns no vouchers**.
>
> **Nothing carries over from Devnet.** Miners and validators have to join testnet again. Devnet earned nothing.
>
> There is still **no launched or tradable mainnet $FLOP token**, and **no claim is live**. Testnet $FLOP is a test token. **Never share your seed phrase** with anyone. Check every link, handle, domain, email and contract against [flop.finance/official](https://flop.finance/official/). FLOP never asks for a seed phrase or a deposit to claim, mint or register anything.
>
> This is an unofficial community guide. Prefer official docs when anything conflicts.

---

## Table of Contents

- [What is this?](#-what-is-this-)
- [What changed from Devnet](#-what-changed-from-devnet-)
- [Prerequisites](#-prerequisites-)
- [Quick Start](#-quick-start-)
- [Specs](#-specs-)
- [Network](#-network-)
- [Validator](#-validator-)
- [GPU Miner](#-gpu-miner-)
- [Agents](#-agents-)
- [Vouchers, models and KOLs](#-vouchers-models-and-kols-)
- [Troubleshooting](#-troubleshooting-)
- [Official links](#-official-links-)
- [Disclaimer](#-disclaimer-)

---

<h2 align="center">⭐ What is this? ⭐</h2>

A community operator guide for **FLOP Labs** on the current **testnet**. Testnet is the pre-launch operating period of the network, and — once scoring is turned on — the work you do here decides your share of the airdrop.

| Role | What you run | GPU? | What official sources say you earn (once scoring is on) |
| --- | --- | --- | --- |
| **Validator** | An RPC node first, then a validator after promotion. No GPU. | No | Ranked on testnet $FLOP earned in block rewards and inference fees from a published block height (~two weeks in) to the end of testnet. The top 1,000 addresses each receive a 1,200,000 $FLOP mainnet stake, unlocking linearly over ten years. One seat per address. Testnet earnings decide rank only — they do not convert into $FLOP, and there is no allocation outside the top 1,000. |
| **GPU Miner** | Miner software on one or more GPUs. Sponsored; no $FLOP needed to register; no GPU cap. | Yes | Two voucher types. **Block reward vouchers** convert into the airdrop one for one. **Inference vouchers** come from inference fees earned, and convert pro rata by total inference served on testnet. Earnings count from the first block (once scoring is on). |
| **Agent** | A wallet / agent identity. No approval and no waitlist. | No | Claim testnet $FLOP from the faucet every day, then **spend it on inference**. Each testnet $FLOP spent on inference earns one agent voucher. Your share of the agent airdrop is your vouchers divided by all agent vouchers. Unspent faucet tokens earn nothing. |

**Status (as of 24 Nov 2026, ~14:00 Africa/Lagos):**

- **Chain:** live. [@flop_labs](https://x.com/flop_labs/status/2125212060481093834) announced it at 13:56 Africa/Lagos. The [operator hub](https://docs.flop.network/operators?network=testnet) says six founding validators launched the chain and are finalizing blocks.
- **Explorer:** [testnet.flopscan.io](https://testnet.flopscan.io/) — “FLOP Testnet · Unscored”. If the Explorer says it is synchronizing, the chain is still running; the Explorer is a viewer and can fall behind when traffic spikes.
- **Operator setup:** the testnet operator hub still marks **validator and GPU miner setup manuals as unavailable**. Validator activation and the signed operator release have **separate readiness checks**. The live announcement covers **network testing and public read access**. Do not use the old Devnet packages, genesis, faucet or RPC as if they were testnet.
- **Vouchers:** officially **disabled** in this very early window. The earning rules below are from FLOP's 24 November launch announcement; they apply when scoring is turned on. Recheck the operator hub and [flop.finance/official](https://flop.finance/official/) before treating any activity as scored.
- This README is a curated walkthrough. Prefer [docs.flop.network/operators?network=testnet](https://docs.flop.network/operators?network=testnet) and [flop.finance/official](https://flop.finance/official/) when anything conflicts.

Inspired lightly by community FLOP write-ups and structured like a Boundless-style ops README. Branding and layout by Matt.

---

<h2 align="center">🔁 What changed from Devnet 🔁</h2>

- **Nothing carries over.** Miners and validators rejoin testnet. The launch announcement says the steps are the same as before, but you must use **testnet** identity, artifacts and endpoints — not the Devnet ones in the previous revision of this guide.
- **This one can count** (once scoring is on). Devnet earned nothing: no rewards and no airdrop weight.
- **Miners are sponsored.** You do not need any $FLOP to register. There is no cap on how many GPUs you run. Compute on testnet is priced at a fixed rate set below today's market, so nobody can pad a voucher count by bidding prices up.
- **Validators are sponsored** and do not need a GPU. Promotion is in daily batches, with a 14-day grace period so a validator that joins on day 10 starts level with one that joined in the first hour.
- **Agents** claim a daily faucet and earn only by spending on inference.
- **Devnet-only values in the previous guide are outdated**, including:
  - RPC `wss://rpc.devnet.flop.network`
  - genesis `0x99c172c095340ace980631f9123544c6bebd4839f3437e463533422d8be2d631`
  - explorer `https://explorer.devnet.flop.network`
  - faucet `https://faucet.flop.network` (that host still reports `network: "devnet"`)
  - gateway `https://api.flop.network` (that host still reports `network: "devnet"`)
  - package `validator-cs-be91fe83 · 0.3.0-devnet.1` and miner pack `miner-pack-devnet-ts-20261106`
  - administrator-assisted Tailscale / `da-peers.json` enrollment as the current testnet path

Keep those Devnet facts only if you are still practising on Devnet. They do not identify or admit you to testnet.

---

<h2 align="center">📋 Prerequisites 📋</h2>

### Notes

- **Never share** your seed phrase, keystore password, or session token — not with “support”, DMs, or an AI agent. FLOP never asks for your seed.
- Use a **dedicated test wallet**. Do **not** reuse Devnet keys as if they were already admitted on testnet.
- **Do not** use the same account for a validator and a miner.
- **Never** run two validators with the same session keys (simultaneous voting = equivocation).
- Do **not** invent hostnames, image digests, model hashes, peer multiaddrs, faucet URLs or gateway URLs. If an official page has not published a value, treat it as unpublished and wait.
- Stopping Docker Compose **does not** stop VM billing. Delete the instance in your provider console when done.

### Requirements

**All roles**

- [ ] Read the [Testnet operator hub](https://docs.flop.network/operators?network=testnet)
- [ ] Verify every link against [flop.finance/official](https://flop.finance/official/)
- [ ] For setup problems, search [existing GitHub issues](https://github.com/flop-labs/network/issues) first; open a new one only if yours is not already reported. The docs bot at [ask.flop.finance](https://ask.flop.finance) answers most questions.

**Validator / RPC node**

- [ ] A dedicated Linux amd64 host meeting the published hardware profile (below)
- [ ] Docker Engine + Docker Compose v2, once a signed testnet operator release is published
- [ ] One GitHub application per team, using the official validator / RPC template (below)
- [ ] The [validator prep kit](https://docs.flop.network/testnet-validator-prep/)

**GPU miner**

- [ ] NVIDIA GPU(s) meeting the published recommendation (16 GB+ VRAM per unit)
- [ ] Full VM or owned host (the Devnet miner docs warned that some container-only rentals cannot run Compose)
- [ ] Wait for the official testnet miner release / pack — the operator hub still marks GPU miner setup as unavailable

**Agent**

- [ ] A wallet you control, and a way to call the official testnet faucet once it is published (Technocore chat or the faucet API, per the launch announcement)

---

<h2 align="center">🏃 Quick Start 🏃</h2>

### Path A — Validator (RPC node, then GitHub application)

The launch announcement: start by running an RPC node, then open an issue on the Flop GitHub using the validator template. Nodes are promoted to validators in daily batches.

1. Read the [validator prep kit](https://docs.flop.network/testnet-validator-prep/) and the [operator intake notes](https://github.com/flop-labs/network/blob/main/OPERATOR-INTAKE.md).
2. Prepare one dedicated Linux host. Do not generate or sign with testnet validator keys until official docs say the runtime / release is frozen and publish the inputs to use.
3. When a signed testnet operator release and chain identity are published, sync a **keyless** RPC node against the official public RPC and the published genesis (see [Network](#-network-)). Keep RPC and metrics on loopback unless you are later admitted to a public RPC pool.
4. Open **one** application per team: [Validator / RPC operator application](https://github.com/flop-labs/network/issues/new?template=operator-application.yml). Select **testnet** and **validator candidate** (or “validator candidate and RPC contributor”). Update that issue instead of opening a duplicate.
5. Wait for daily-batch promotion. Registration is not a seat. Applying, syncing and RPC pool participation do not grant activation, funding, rewards or a testnet seat.

Details: [Validator](#-validator-)

### Path B — GPU Miner

The launch announcement: miners are sponsored (no $FLOP needed to register), there is no GPU cap, and earnings count from the first block once scoring is on. Expect a calibration queue on day one. If the endpoint says “try again later”, you have not been turned away — keep calling until a spot opens.

1. Prepare an NVIDIA host (16 GB+ VRAM per GPU).
2. **Wait for the official testnet miner pack / signed operator release.** As of this writing the testnet operator hub still lists GPU miner setup as unavailable. Do **not** point the old Devnet miner pack at testnet.
3. When the pack is published, verify its checksums, follow its README, register, and calibrate.
4. If calibration is queued, retry later. Do not invent an alternate endpoint.

Details: [GPU Miner](#-gpu-miner-)

### Path C — Agent

1. Wait for the official testnet faucet and gateway to be published. The currently listed `faucet.flop.network` and `api.flop.network` hosts still identify themselves as **devnet**.
2. Claim testnet $FLOP from the official faucet every day (Technocore chat message or faucet API, once those testnet routes are published).
3. Spend it on inference. Unspent tokens earn nothing, and nothing is scored while official pages say voucher earning is disabled.

Details: [Agents](#-agents-)

---

<h2 align="center">🖥 Specs 🖥</h2>

### Validator

From [flop.finance/testnet](https://flop.finance/testnet/) (provisional) and the [validator prep kit](https://docs.flop.network/testnet-validator-prep/):

| Resource | Official recommendation |
| --- | --- |
| CPU | 8+ cores (prep kit: eight physical cores at 3.4 GHz or faster) |
| RAM | 64 GB recommended (prep kit: 32 GB ECC minimum, 64 GB recommended) |
| Storage | 4 TB NVMe (flop.finance: 2 TB minimum) |
| Network | 1 Gbps symmetric / redundant connection |

These are official recommended specifications and may be refined. Record the capacity you actually have.

### GPU Miner

| Resource | Official / published notes |
| --- | --- |
| GPU | A single GPU, or a cluster, with **16 GB+ VRAM per unit** ([flop.finance/testnet](https://flop.finance/testnet/)). No cap on how many GPUs you run. |
| Host | Full VM or owned Linux host once the testnet miner release is published |

Need to rent **20 GPUs or more**? FLOP's launch announcement said they can introduce you to the institutional data center providers they work with. Use the contact path in that official announcement — do not mail a random inbox, and check any address against [flop.finance/official](https://flop.finance/official/).

---

<h2 align="center">🌐 Network 🌐</h2>

Recheck [flop.finance](https://flop.finance/), [flop.finance/official](https://flop.finance/official/) and the [testnet operator hub](https://docs.flop.network/operators?network=testnet) before you sign anything. The launch announcement said endpoints and the first-block hash would be published on flop.finance. As of ~14:00 Africa/Lagos on 24 Nov 2026, **flop.finance/official was still last updated 20 Nov 2026** and still described testnet as planned. The values below are only what official surfaces had actually published, or what the official public RPC itself returned, at that time.

### Published testnet surfaces

| Setting | Value | Source |
| --- | --- | --- |
| Explorer | https://testnet.flopscan.io/ | [@flop_labs launch post](https://x.com/flop_labs/status/2125212060481093834), [operator hub](https://docs.flop.network/operators?network=testnet) |
| Public RPC | `wss://testnet.flop.network/` | [operator hub](https://docs.flop.network/operators?network=testnet) (same host is also in the explorer's `connect-src`) |
| Official links page | https://flop.finance/official/ | Canonical list of accounts, domains and the one public email |
| Docs / operator hub | https://docs.flop.network/operators?network=testnet | Operator docs |
| Docs bot | https://ask.flop.finance | Launch announcement and official links page |
| GitHub | https://github.com/flop-labs | Official accounts list |

### Chain identity observed from the official public RPC

These were **not yet written on flop.finance** when this guide was updated. They were read from the official public RPC `https://testnet.flop.network` (JSON-RPC `POST`, same host as the published `wss://` endpoint) at ~14:00 Africa/Lagos on 24 Nov 2026:

| Field | Value |
| --- | --- |
| `system_chain` | `FLOP Testnet` |
| `system_chainType` | `Live` |
| `system_properties` | ss58 format `42`, 18 decimals, token symbol `FLOP` |
| First block / genesis hash (`chain_getBlockHash` with `[0]`) | `0x918abd58fb16306b30ef3886514cfaebffecb4de43e82b6f8ec4795c43cfd800` |
| `system_health` (at check time) | `isSyncing: false`, `peers: 8` |
| Best head number (at check time) | `0x98f` (2447) |

Compare your own node's `chain_getBlockHash` with `[0]` to that genesis before you sign or fund anything. If flop.finance later publishes a different first-block hash, **trust the official page**, not this README.

A file SHA-256 and a genesis hash are different values — do not compare them to each other.

### Not yet published for testnet (do not invent them)

As of this writing, official docs and [flop.finance/official](https://flop.finance/official/) had **not** listed a testnet faucet, API gateway, bootnodes, raw chain spec, or signed operator release. The [testnet network status](https://docs.flop.network/docs/network-testnet) page had also not been updated off “planned”.

These currently published hosts still identify as **devnet**, not testnet:

| Host | What it reported |
| --- | --- |
| `https://faucet.flop.network/chain` | `"network": "devnet"`, genesis `0x99c172c095340ace980631f9123544c6bebd4839f3437e463533422d8be2d631`, RPC `wss://rpc.devnet.flop.network` |
| `https://api.flop.network/health` | `"network": "devnet"` and the same devnet genesis |

Do not send testnet keys, claims or spend to those Devnet hosts. Wait for official testnet faucet and gateway URLs.

---

<h2 align="center">🏅 Validator 🏅</h2>

Official pages to follow:

- [Testnet operator hub](https://docs.flop.network/operators?network=testnet)
- [Validator prep kit](https://docs.flop.network/testnet-validator-prep/)
- [Operator intake](https://github.com/flop-labs/network/blob/main/OPERATOR-INTAKE.md)
- Application form (the official validator template): [operator-application.yml](https://github.com/flop-labs/network/issues/new?template=operator-application.yml)

The older [Run a validator](https://docs.flop.network/docs/run-validator) page is still a **Devnet** manual (last updated 12 Nov 2026) and says those instructions do not configure testnet. The testnet operator hub still marks the validator setup manual as unavailable, and says validator activation and the signed operator release have separate readiness checks.

### How admission works (from the launch announcement and intake docs)

1. **Run an RPC node** (keyless first). Validators are sponsored and do not need a GPU.
2. **Open one GitHub issue** with the [validator / RPC operator application](https://github.com/flop-labs/network/issues/new?template=operator-application.yml). Select network **testnet** and role **validator candidate**. One application per team per network; update the existing issue rather than filing a second one. The issue is public — never upload seeds, private keys, `account.json`, `.env`, keystores, enrollment tokens or TLS private keys.
3. FLOP **promotes nodes to validators in daily batches**. The set grows in steps so that no single party can take over the network in the first days.
4. **14-day grace period.** FLOP will publish a block height about two weeks in. The ranking for mainnet counts only what your validator earns **from that block to the end of testnet**. A validator that joins on day 10 starts level with one that joined in the first hour.
5. The **1,000 validators** that earn the most testnet $FLOP in block rewards and inference fees each receive the **1,200,000 $FLOP** stake for a mainnet seat, unlocking linearly over ten years. Every address is ranked on its own earnings and holds one seat, so you can run as many validators as you like, but each address still gets at most one seat. Tokens that were bought or transferred in are left out of the ranking. Testnet earnings decide rank and nothing else — they do not convert into $FLOP, and there is no allocation outside the top 1,000.

Team registration is open now. Seat assignment, sponsorship and activation are separate steps. No payment or signature is due just to register.

### What to wait for before signing

The prep kit and operator hub are explicit: wait for the approved raw chain spec and checksum, genesis hash, pinned images, bootnodes and coordinated start / release inputs. Do not generate fresh testnet BABE, authority-discovery, Aleph or P2P node keys, and do not sign a ceremony packet, until official docs say the runtime is frozen and publish those inputs.

When a signed testnet release is published:

- Verify archive checksums yourself.
- Compare local `chain_getBlockHash` with `[0]` to the official first-block hash (and to the hash in [Network](#-network-) only if official pages have not superseded it).
- Keep RPC and metrics on loopback. A public RPC origin must stay keyless; never add validator keys to a public RPC origin.
- If a signed submission fails or is partial, **stop**. Do not blindly rerun signed actions.
- “Ready” is not the same as “active”. Confirm session keys and advancing finality after promotion.

### Devnet rehearsal (optional, still earns nothing)

The prep kit still recommends a keyless Devnet RPC rehearsal as host practice. That is Devnet only. Devnet evidence does not qualify a testnet node, and Devnet activity has no airdrop weight.

---

<h2 align="center">🎮 GPU Miner 🎮</h2>

The official [Run a miner](https://docs.flop.network/docs/run-miner) page is still a **Devnet** manual (last updated 12 Nov 2026) and says those instructions do not configure testnet. The testnet operator hub still lists GPU miner setup as unavailable. **There is no official testnet miner pack to pin in this guide yet.**

### Rules from the launch announcement

- Miners are **sponsored**; you do not need any $FLOP to register.
- There is **no cap** on how many GPUs you run.
- Earnings count from the **first block** (once scoring is on).
- You earn two kinds of voucher:
  - **Block reward vouchers** convert into the airdrop one for one.
  - **Inference vouchers** come from the inference fees you earn, and convert according to the total inference served across testnet, shared in proportion to the inference vouchers each miner holds.
- Expect a **calibration queue on day one**. If it is full, the endpoint tells you to try again later. That is not a rejection — keep calling until a spot opens.
- Compute is priced at a **fixed rate** set below today's market, so nobody can pad their voucher count by bidding prices up.

### When the official testnet pack appears

1. Download it only from a link published on [flop.finance/official](https://flop.finance/official/), [docs.flop.network](https://docs.flop.network/operators?network=testnet), or another surface listed on the official links page.
2. Verify the published SHA-256. `FAILED` means delete and re-download.
3. Follow **that** pack's README for account creation, sponsorship, compose, registration and calibration. This community guide will not invent image digests or command sequences that official docs have not published for testnet.
4. Keep seeds, keystore passwords and `SESSION_AUTH_TOKEN` **on the host** — never paste them into chat.
5. If calibration returns “try again later”, retry; do not loop against a rate limit, and do not invent another endpoint.

The previous revision of this guide pointed at `miner-pack-devnet-ts-20261106` and the Devnet Qwen2.5-0.5B drill. Those are **Devnet-only** and must not be used as testnet instructions.

---

<h2 align="center">🤖 Agents 🤖</h2>

From the launch announcement:

- There is **no approval and no waitlist**.
- Your agent claims testnet $FLOP from the **faucet every day**, either with a message in Technocore chat or with a call to the faucet API.
- **Spend it on inference.** Each testnet $FLOP your agent spends on inference earns one agent voucher.
- When testnet ends, your share of the agent airdrop is your vouchers divided by all agent vouchers.
- Faucet tokens that sit unspent earn nothing.

**Right now:** official pages have not yet published a testnet faucet URL or testnet gateway URL, and they say voucher earning is disabled in this very early window. `faucet.flop.network` and `api.flop.network` still report **devnet**. Do not assume a Devnet faucet grant or gateway allowance counts on testnet.

When those endpoints are published, compare the faucet's chain identity (genesis / network name) with the official testnet genesis before you claim or spend.

---

<h2 align="center">🎫 Vouchers, models and KOLs 🎫</h2>

**Vouchers** are the record of what you earned on testnet. At mainnet they convert into your airdrop, by the agent and miner rules above. Validator rank is separate (top 1,000 by earnings).

You will also be able to **buy and sell vouchers before mainnet**. Trades will run peer to peer through the protocol's own HTLC atomic swaps, so neither side has to trust the other. The market sets the price; FLOP makes no rules on it. Buying and selling will **not** be available on day one — the feature is planned within several weeks of the start, and will be announced when it is live. The official explorer and operator hub currently say trading is disabled.

**Models:** anyone can upload a model to Flop Network. There is no request to file and no approval to wait for. Uploading takes a refundable bond in testnet $FLOP, sized to the model. Wait for the official testnet upload procedure before sending a bond.

**KOLs:** the key opinion leader leaderboard starts at **mainnet**. Nothing is scored during testnet, and you still don't need to sign up or apply.

---

<h2 align="center">🩺 Troubleshooting 🩺</h2>

| Symptom | Likely cause | What to do |
| --- | --- | --- |
| Official pages still say testnet is “planned” | Docs lag behind the live announcement | Trust the [operator hub](https://docs.flop.network/operators?network=testnet), [flop.finance/official](https://flop.finance/official/) and [@flop_labs](https://x.com/flop_labs). Recheck before signing. |
| Explorer says “synchronizing” | Viewer lag, not necessarily a halted chain | The launch announcement: the Explorer is a viewer and can fall behind when traffic spikes. Check the official public RPC as well. |
| No testnet faucet / gateway / miner pack | Not published yet; features are still being activated | Wait for official URLs. Do not invent hostnames or reuse Devnet hosts. |
| Calibration says “try again later” | Day-one queue is full | Not a rejection. Keep calling until a spot opens. |
| `sha256sum: FAILED` | Corrupt or wrong download | Delete the archive; re-download from the official link; re-check. |
| Setup problem, unclear error | Local misconfig or an unpublished path | Search [existing issues](https://github.com/flop-labs/network/issues) first, then open a new one if needed. Ask [ask.flop.finance](https://ask.flop.finance). |
| Anyone asking for your seed, a “claim”, or a token contract | Scam | There is no mainnet $FLOP token and no claim is live. Check [flop.finance/official](https://flop.finance/official/). Report impersonations to the security address listed there. |

---

<h2 align="center">🔗 Official links 🔗</h2>

Verify every link against [flop.finance/official](https://flop.finance/official/) before you trust it.

- Official links (canonical): https://flop.finance/official/
- Project site / testnet page: https://flop.finance/ · https://flop.finance/testnet/
- Operator hub (testnet): https://docs.flop.network/operators?network=testnet
- Testnet network status: https://docs.flop.network/docs/network-testnet
- Validator prep kit: https://docs.flop.network/testnet-validator-prep/
- Validator / RPC application (official template): https://github.com/flop-labs/network/issues/new?template=operator-application.yml
- Operator intake: https://github.com/flop-labs/network/blob/main/OPERATOR-INTAKE.md
- Docs assistant: https://ask.flop.finance
- Testnet explorer: https://testnet.flopscan.io/
- Public RPC: `wss://testnet.flop.network/`
- FLOP on X: https://x.com/flop_labs
- GitHub: https://github.com/flop-labs
- Security contact (the only official email, listed on the official links page): the address published at [flop.finance/official](https://flop.finance/official/)

Devnet manuals (outdated for testnet setup, kept here only as references): [Run a validator](https://docs.flop.network/docs/run-validator) · [Run a miner](https://docs.flop.network/docs/run-miner)

---

<h2 align="center">📜 Disclaimer 📜</h2>

This is an **unofficial community guide** by Matt (@mattdreams / GitHub [0andadream](https://github.com/0andadream)).

- Testnet $FLOP is a test token and has no monetary value. There is no launched mainnet $FLOP token and no claim is live.
- In this very early window, official pages say the network is unscored and voucher earning is disabled. Do not treat this guide as a promise of earnings or of an airdrop allocation.
- Commands, packages, endpoints and genesis pins change. Always cross-check [docs.flop.network](https://docs.flop.network/operators?network=testnet), [flop.finance/official](https://flop.finance/official/) and published checksums before signing or spending.
- You are responsible for your keys, host security, and cloud spend.
- MIT-licensed docs in this repo; FLOP protocol / images / packages remain their respective owners'.

---

<div align="center">

Built by [Matt](https://x.com/mattdreams) · GitHub [0andadream](https://github.com/0andadream) · MIT

</div>
