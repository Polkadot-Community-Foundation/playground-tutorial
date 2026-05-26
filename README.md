# Playground Tutorial — Rock Paper Scissors

A guided, hands-on tutorial that takes a plain web app and turns it into a full on-chain product on Polkadot, one step at a time. You start with a local Rock Paper Scissors game and unlock a new layer of the stack at each level.

You stay on `main` the whole time — every level builds on top of the previous one in the same working tree. No branch hopping, no separate checkouts.

## How to start

```bash
dot mod <your-name>.dot
```

The CLI fetches `quests.json`, shows you the level picker, clones the repo, and drops you into the project. From there, follow the level you're on.

## Levels

Each level is described in [quests.json](quests.json). AI context for each step lives in [.claude/skills/](.claude/skills/).

### Level 1 — Local Challenger _(~15 min, ★)_

The starting point. Play best-of-3 vs. the computer, results saved in `localStorage`. Mod the UI (theming, emoji sets) or behavior (computer "personality", trash-talk, sound effects), then ship it to your `.dot` domain via `dot deploy`. No contracts, no chain — just product.

### Level 2 — On-Chain Record _(~20 min, ★★)_

Move game history off the device. Results get uploaded to Bulletin Chain as JSON; the resulting CID is your portable game record. The latest CID per account is tracked in `localStorage` (`rps-game-cid:<address>`) so the app can rehydrate the profile on reload from any client.

### Level 3 — The Leaderboard _(~25 min, ★★★)_

Deploy a PVM smart contract via CDM. Now `address → (CID, points)` lives on Paseo Asset Hub. Anyone can look up any player's history through the leaderboard UI. This is the level where Bulletin (off-chain storage) and the contract (on-chain index) start working together.

### Level 4 — Multiplayer _(~30 min, ★★★★)_

Real PvP. Create a room, share a link or QR code, opponent joins, you play live over Statement Store with commit-reveal so neither side can cheat by peeking at the other's move. Both leaderboard entries update at the end.

## Tech Stack (by level)

| Layer            | Introduced in | Technology                                     |
| ---------------- | ------------- | ---------------------------------------------- |
| UI               | Level 1       | React 19 + Vite + TypeScript                   |
| Wallet           | Level 1       | `@polkadot-apps/signer` (Host API)             |
| Hosting          | Level 1       | `dot deploy` → Bulletin + DotNS                |
| Off-chain store  | Level 2       | `@polkadot-apps/bulletin` (CID-addressed JSON) |
| Smart contract   | Level 3       | `@dotdm/cdm` + PVM (Rust) on Asset Hub         |
| Live multiplayer | Level 4       | `@polkadot-apps/statement-store`               |

## Running locally

```bash
npm install
npm run dev
```

Runs on `http://localhost:5173`. Must be opened inside a Polkadot Desktop container for Host API login.

> Levels 2–4 need PAS tokens on Asset Hub ([faucet](https://faucet.polkadot.io/)) and on Bulletin chain ([faucet](https://paritytech.github.io/polkadot-bulletin-chain/authorizations?tab=faucet)).

## Structure

```
quests.json                  # Level manifest (read by the CLI)
.claude/skills/              # Per-level AI context (loaded by Claude)
src/
├── App.tsx                  # Routing + account selector
├── utils.ts                 # SignerManager + localStorage helpers
├── types.ts                 # Move, Round, GameData, PlayerData
└── pages/
    ├── Home.tsx             # Mode picker + profile
    ├── MyProfile.tsx        # Stats + history
    └── SoloGame.tsx         # Solo match vs computer
```
