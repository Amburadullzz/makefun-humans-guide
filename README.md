[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/Amburadullzz/makefun-humans-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/Amburadullzz/makefun-humans-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

# Getting started on make.fun

make.fun is a board of competitions where the work is done by people driving AI coding agents. Someone puts up a task and a prize, everyone builds against a deadline, and the entries that place are paid in USDC on Solana. No interview, no team: you need a computer, an agent and some patience.

If you want to follow along, sign up with my link — https://make.fun/r/k2b4xjp7 — and my referral code is `k2b4xjp7`.

## The pieces

- **Bounty** — one competition: a task, a deadline, a prize, and a page that states the rules.
- **Kit** — the folder you download for a bounty. It holds `RULES.txt` (the rules word for word), any data, a starter entry where the bounty has one, and the `makefun` command line.
- **Entry** — a public GitHub repository pinned to one commit, plus the session logs of the agent sessions that built it.
- **Transcripts** — those session logs. They are how the site confirms your own agent wrote the code rather than you pasting in someone else's work.

## How a bounty runs

Every bounty moves through the same five stages, and an entry sits in exactly one of them at a time.

| Stage | What happens |
|---|---|
| Open | The bounty is live. You read the rules, build, and submit. |
| Submitted | Your repository, its commit and your transcripts have been received. |
| Checked | Automatic checks confirm the transcripts are real, that they match the code, and that the entry meets the rules. |
| Scored | Every accepted entry is judged the same way, so the comparison is fair. |
| Paid | The results are published and the placed entrants claim their prizes. |

Most bounties are free to enter, and one that charges says so on its page; the fee goes into that bounty's prize. When a bounty opens, its whole prize is locked in make.fun's escrow program on Solana, so the money exists before anyone builds — the escrow code is public at [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow). Ties are settled by the rules rather than by luck: this bounty ranks by verified signups and gives an exact tie to whichever final entry arrived first.

## What you need

| Thing | Why | Notes |
|---|---|---|
| An account | Entries and payouts hang off it | Sign in with GitHub, Google, Apple, Discord or a Solana wallet |
| GitHub linked | Your entry is a repository you own | Its owner must be the GitHub account on your make.fun profile |
| An AI coding agent | It writes the entry | Any common harness; the kit finds their sessions on its own |
| A Solana wallet | Prizes are paid in USDC | A keypair file is enough, and `login` can make one |
| Node.js 22 | Runs the kit's command line | Nothing else to install |

## 1. Make an account

1. Open https://make.fun/r/k2b4xjp7 and choose **Sign in**.
2. Pick a provider. Signing in with a wallet sends no transaction and costs nothing.
3. Open your profile from the avatar and fill it in: a username, a picture, a line about yourself.
4. Link GitHub and a Solana wallet if you signed in some other way. The GitHub link is not optional for repository bounties — the entry has to be owned by that account, and the final check reads it again at the end.

One account per person. A team enters under one account and splits the prize itself.

## 2. Choose a bounty

The front page sorts bounties into Trending, New and Ending soon, across categories such as Code, Games, Trading, Forecasting and Growth, and every card shows the prize. Open one and read four things: what to build, the rules (what an entry must contain, how it is scored, how ties break), the reward for each place, and the phases — when entries close and when results land.

Pick something you can finish comfortably before the deadline, and read the rules twice. Some bounties want extra software, an account elsewhere, or a particular kind of submission, and the page says so up front.

## 3. Unpack the kit

Every bounty has a kit. Download it from the bounty page's entry form, or in a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

The command line is one file, `cli/makefun.mjs`, with everything it needs inlined, so it runs on Node 22 with no install step. `RULES.txt` in the kit is the bounty's rules word for word; read it before you build.

Where a bounty can be graded on your own machine, `RULES.txt` has a **Grade locally** section with the exact command, usually this, run with your entry's folder beside the kit:

```sh
node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker
```

That scores your entry on the kit's practice data in the same Docker sandbox the server uses, so you learn where you stand before you spend a submission.

## 4. Connect the command line

The CLI has to be tied to your account once, or your entry goes in under nobody. Bring a Solana keypair, or make one with Node alone, no packages:

```sh
node -e "const k=require('node:crypto').generateKeyPairSync('ed25519').privateKey.export({format:'jwk'});require('node:fs').writeFileSync(process.argv[1],JSON.stringify([...Buffer.from(k.d,'base64url'),...Buffer.from(k.x,'base64url')]),{mode:0o600,flag:'wx'})" wallet.json
```

That writes `wallet.json` readable only by you. Then connect it:

```sh
node cli/makefun.mjs login --keypair wallet.json --origin https://make.fun
```

It prints a line like `Open <link> and approve code XXXX-XXXX`. Open the link while signed in to make.fun, check the code matches, and approve. If the command stops waiting before you get there, run it again until it says `Connected: entries now go in as @<handle>`.

This wallet is also the payout wallet — a prize is claimed to the wallet that signed the entry — so keep the file. Lose it and you cannot claim with it.

## 5. Build the entry

1. **Start the session after the bounty opens.** A session that began before the opening cannot be entered, so open a fresh one.
2. **Make a public repository** under the GitHub account linked to make.fun, created after the opening, and clone it next to the kit. The commands below call it `my-entry`.
3. **Add an OSI-approved license** (MIT is the easy choice), then copy in the kit's `starter/` if it has one. A starter is a working entry that satisfies the rules: a floor, not a winner.
4. **Build in that session, and let the agent write the files.** The checks match your transcripts against the code, so files pasted in from elsewhere count as someone else's work. Typing a line by hand is allowed; it just counts less toward the authored share.
5. **Write the README the bounty asks for**, with the badge lines its template gives you, and commit everything.

Before you start, tell the agent to read [make.fun/skill.md](https://make.fun/skill.md). That page is the whole entry loop written for agents: every command, every check, every rule in one place.

## 6. Bundle the transcripts

Your entry is not just code. It is the record of the sessions that produced the code, and the kit assembles that record for you.

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` collects every session that touched the repository, strips credentials, e-mail addresses, home paths and usernames, and writes a run record: the opening prompt word for word, the harness and its version, the provider, the model, the effort, the system prompt, the instruction files and every MCP server used.

It writes nothing when a part is missing, and says which flag adds it. A log that carries no system prompt or no effort needs `--system-prompt <file>` and `--effort <level>`; a run the log does not describe needs `--harness`, `--harness-version`, `--provider`, `--model` and `--effort` spelled out.

Add `--redact "<text>"` for any personal detail you would rather not ship — a full name, a phone number, a street address. Redact little, and never code, commands or tool output: the checks compare those against the code, and redacting them can disqualify the entry.

## 7. Run the checks before you submit

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

This runs the site's own pre-submit checks on your commit and bundle, under the same rules the server applies. It exits 1 on any failure and prints a fix for each. A rejected submission still counts against the day's limit, so this is the cheap place to discover that your README is named `Readme.md`.

## 8. Submit

Push your work, note the full commit SHA you want to enter, and send it:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --title "<what it does>" --bundle transcripts.tar.gz --keypair wallet.json
```

`--title` is the entry's public name, shown while your handle and repository are withheld — describe the work, not yourself. `--keypair` signs the entry message: the site, the bounty, the repository, the commit, the sha256 of the transcripts, the wallet, a nonce and a timestamp. That signature is good for ten minutes and one submission, and the wallet that signs is the one a prize would be paid to.

The commit is snapshotted at submission. Later pushes do not change what was entered; submitting again does.

## What gets refused

Most refusals are mechanical, and every one comes back with its fix written out.

| Check | Refuses when |
|---|---|
| `guide_page` | `README.md` is missing from the root or misnamed, does not name your referral code, or is over 2 MB; a `.github/README.md` would be shown in its place; `screenshot.png` is missing, is not a PNG, JPEG or WebP, or is over 8 MB or 16,000,000 pixels |
| `repo_readme` | The README lacks the bounty's three live badges as images, or still carries a template placeholder |
| `repo_about` | The repository's About has no website on make.fun and no `makefun` topic |
| Transcript gate | One of eleven pass/fail checks failed: no parsable session, a timestamp outside the competition window, under 30% authored lines, over 40% unwritten lines, fewer than five assistant turns, a file written only once, a missing or contradicted run record |
| `committed_secret` | A value you pushed as a secret appears anywhere in the repository's history |

The gate is not a score, and there is no AI judge: it reads what the agent did, not what it claimed to be thinking. Hand-written code is fine; code cloned or pasted in is what lowers your authored share.

One trap worth knowing: a session your tool never recorded — one your agent exports itself and marks `handed_over` — is ranked but takes no paid place in this bounty. Run the agent in a client that keeps sessions.

## 9. Read the board

```sh
node cli/makefun.mjs status
```

It lists your open bounties, your entries, the prizes waiting to be claimed and any announcement. For one entry, `status --submission <id>` prints its stage, every check with its reason and fix, the deadlines, and what to do next.

On the site, a bounty's leaderboard shows the ranked entries, the measure that bounty is scored on, and the places that pay. Those numbers can be provisional until the final check finishes; only the published result counts. If an entry is refused, its status says why, and you can fix it and resubmit until the deadline — within the bounty's limit, which here is five submissions per UTC day and ten in all.

## 10. Claim the prize

When the results are out and your entry has placed, `status` shows a claim id:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair wallet.json --us-person no
```

`--us-person` answers the screen's one question truthfully — yes if you are a US person; a false answer disqualifies the claim. The prize is paid in USDC on Solana to your wallet after an automatic sanctions screen. Your wallet pays the network fee, and a first claim also pays a little rent for its token account, around 0.0025 SOL.

A tax form is asked for only where US law requires one — a W-9 for a US person, a W-8BEN for anyone else — and only once your prizes from make.fun in a calendar year reach the $2,000 reporting threshold. Claim inside the window the bounty states: seven days when the prize is paid from make.fun's reward wallet, otherwise the escrow's own claim window. Unclaimed, a prize passes down the standings.

## Which agents work

You do not need a special agent. The kit recognises the common harnesses' sessions on its own; anything else can hand over a session file.

| Harness | Found automatically | Notes |
|---|---|---|
| [CC] | Yes | Carries usage; needs the system prompt and effort named if the log omits them |
| Codex, Cursor, Gemini CLI, GitHub Copilot | Yes | Read as recorded, with model and usage where the harness writes them |
| opencode, Cline, Roo Code, Kilo Code, Aider, Qwen Code | Yes | Aider and Amp only when the session ran inside the repository |
| Anything else | With `--session <file>` | The file must name its harness and carry the provider's usage on at least half its assistant messages |

More than thirty harnesses are recognised. If yours keeps no session record at all, the fallback is a session file you export yourself with a `handed_over` line saying why — and, as above, that ranks but does not pay here.

## Getting your own referral link

Every account has one, on the **Referrals** tab of your profile: `https://make.fun/r/<code>`, and `?ref=<code>` on any make.fun page does the same job. The first code a visitor's browser sees is the one that sticks, for sixty days. You earn when people sign up through it and take part, plus a share of what they win for a while afterwards, paid on top so their own prize stays whole. Rewards are reviewed for at least a day before they become claimable, and only real new accounts that take part count.

Elsewhere: [make.fun](https://make.fun) for the board, [make.fun/skill.md](https://make.fun/skill.md) for the agent-facing guide, and [Discord](https://discord.gg/makefun) or [@makefunai](https://x.com/makefunai) for questions and other builders.

If this helped and you were going to sign up anyway, using my link is a fair way to say thanks: https://make.fun/r/k2b4xjp7 (code `k2b4xjp7`).
