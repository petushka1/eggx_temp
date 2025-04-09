# 🧭 EggX Protocol – Project Roadmap

Welcome to the long-term roadmap and development outline of the **EggX Token Sale Platform**.

## Phase 1 – Hackathon MVP

**Timeframe:** Weeks 1–3  
**Goal:** Deliver a working end-to-end MVP for token sale with frontend + smart contract.

### ✅ Deliverables:

- [x] Deploy EGGX token to Sui Devnet
- [x] Build `token`, `sale`, and `buy` contracts
- [x] Dynamic price logic: `SoldRatioScaled`
- [x] Reserve + burn mechanics
- [x] Fully functional frontend (ReactJS)
- [x] Countdown + Stage Switch with `Clock` and `init_stage`

<br> `↓ ↓ ↓`

## Phase 2 – Protocol v1

**Timeframe:** Month 2  
**Goal:** Transform MVP into a reusable protocol with customizable logic.

### 🛠 Tasks:

- [ ] Modular sale contract with pluggable price models
- [ ] Add dynamic object fields to support:
  - Sale status tracking
  - Multiple currencies (optional Table)
- [ ] Frontend SDK (React Hooks) for no-code integration
- [ ] Countdown SDK:
  - Start/stop, pause/resume, extend/shorten
  - Auto-callback to `init_stage` when timer ends

<br> `↓ ↓ ↓`

## Phase 3 – Launch Dashboard + Token Creator

**Timeframe:** Month 3+  
**Goal:** Provide a UI-based, no-code experience for token launches.

### ✨ Features:

- [ ] Web Dashboard (React):
  - Token creator
  - Sale stage configurator
  - Price model selector
  - Deployment flow with wallet
- [ ] Backend light API (optional in Python for now)
- [ ] Automatic `Move.toml` generator
- [ ] CLI deployment via SUI or `npx eggx-launcher` <br><br>

## Long-Term Goals

- [ ] Support custom utility/gov/loyalty token use cases
- [ ] Add Action enum to enable scenarios:
  - Buy
  - Claim reward
  - Auto distribution
- [ ] Cross-chain liquidity bridges
- [ ] NFT + SFT support in Phase 4
- [ ] DAO integration (proposal → sale setup)

## ✨ Ideas for Open Source Utilities

### EggX Countdown SDK

> A small package to let anyone build on-chain + off-chain synced countdowns with `Clock`, with:

- Configurable timer window
- Callback injection on end
- Frontend-react-ready

---

### Contributing

If you're a builder or want to help design the full-stack protocol, feel free to reach out via [issues](https://github.com/petushka1/eggx/issues) or on [X](https://x.com/EggX_). <br> <br>

---

_Made with 🥚 and Move by the EggX team._
