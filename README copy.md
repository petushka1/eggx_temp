# 🪙 Egg-X RWA Tokenization Protocol

<img src="frontend/src/assets/symbol.svg" width="180" title="hover text">

## About the Project

### Overview

Egg-X is a blockchain-based protocol built on the SUI network, aimed at helping startups, digital service providers, and brands tokenize their products and create new crowdfunding opportunities. The platform enables businesses to launch and sell fungible tokens that represent their products or services, allowing them to raise funds, pre-sell goods, and manage supply and pricing efficiently.

### Domain and Social Media

- **Domain**: [e99x.com]() _(in progress...)_
- **Social**: [@EggX\_](https://x.com/EggX_)

### Features

- **Dashboard**: Zero-coding contract deployment.
- **Customization**: Flexibility to adjust parameters like token pricing, supply distribution, and sales phases.
- **Third-party Integration**: Easily integrated into existing platforms for seamless token sales.

Egg-X empowers projects to:

- Pre-sell tokens representing future products at a fixed price.
- Raise capital transparently via blockchain-based crowdfunding.
- Dynamically manage token supply through burning and price adjustments.
- Ensure fair distribution and stability with reserve mechanisms.
- Integrate with DeFi for further token utility and liquidity.

### Product Concept

In the initial phase, we focus on fungible tokens (monetary units) that businesses can use to tokenize their goods or services. The system allows for flexible sale stages and dynamic pricing.

A company can:

- Define a price for its product in SUI.
- Sell tokens in multiple phases, increasing the price gradually.
- Use reserves and burning mechanisms to stabilize the tokenomics.
- Allow consumers to easily purchase tokens.
- Provide an embedded purchase form for third-party web integration.

_Over time, the protocol will expand to support NFT and semi-fungible tokens, DeFi features, secondary market trading, and broader payment integrations._

---

## Hackathon Entry – What We Are Building

1. **Token Launch Contract**  
   A smart contract that creates, mints and burns the Egg-X native token. This will allow building the community from the very beginning.

2. **Token Sale Contract**  
   A modular, multi-phase crowdfunding contract that enables projects to sell tokens in timed stages. Supports:

   - Dynamic pricing logic (Fixed Step, Burn Scaled, Sold Ratio Scaled)
   - Manual or frontend-triggered stage initialization
   - Treasury cap-based minting, reserve management, burning and price updates  
     This contract ensures structured distribution and sustainable tokenomics across each phase.

3. **Frontend Interface**  
   A lightweight, no-backend React interface that interacts directly with on-chain contracts. It allows:
   - Token purchase with wallet integration (SUI)
   - Stage tracking with countdown timers
   - Pricing visibility and sale progress  
     Includes hooks for automated stage transitions using `useEffect`, aligned with on-chain stage logic.

---

🛠 **Tech Stack**

- **Smart Contracts**: SUI Move
- **Frontend**: ReactJS
- **UI**: Lightweight component system (TBA)

---

## Tokenomics

The **Egg-X Token** is the internal utility token used for platform services, including product launches and tokenized sales.

- **Maximum Supply**: 120 million EGGX
- **35% reserved** for the team, community, and development.
- **65% allocated** for sale in six phases over six months.
  - **20 million tokens** released per phase, split as follows:
    - 13M for direct sale.
    - 7M reserved for ecosystem support and future use.
- **Burning Mechanism**: Unsold tokens, including the remainder from the 35% reserve, are burned after each phase, reducing total supply and increasing scarcity.
- **Dynamic Pricing**: After each sale stage, the Egg-X token price increases proportionally based on the amount burned and a custom multiplication coefficient, ensuring early buyers benefit.
- **SUI Payments Accepted**: Users can buy Egg-X tokens using SUI, boosting liquidity and adoption.

### EGGX Dynamic Price Model — SoldRatioScaled (PriceMode #4)

Price Formula:  
`Price = initial_price + (initial_price × increment × sold_ratio)`  
Where:

- `initial_price = 0.1 SUI`
- `increment = 0.5` (i.e. 50%)
- `sold_ratio = tokens_sold / hard_cap`

| Tokens Sold | % of Hard Cap Used | Sold Ratio | Price Calculation                 | Final Price (SUI) |
| ----------- | ------------------ | ---------- | --------------------------------- | ----------------- |
| 0           | 0%                 | 0.00       | 0.1 + (0.1 × 0.5 × 0.00) = 0.1    | 0.1000            |
| 250,000     | 25%                | 0.25       | 0.1 + (0.1 × 0.5 × 0.25) = 0.1125 | 0.1125            |
| 500,000     | 50%                | 0.50       | 0.1 + (0.1 × 0.5 × 0.50) = 0.1250 | 0.1250            |
| 750,000     | 75%                | 0.75       | 0.1 + (0.1 × 0.5 × 0.75) = 0.1375 | 0.1375            |
| 1,000,000   | 100%               | 1.00       | 0.1 + (0.1 × 0.5 × 1.00) = 0.1500 | 0.1500            |

Egg-X provides a scalable, decentralized, and efficient way for businesses to launch and monetize their products through tokenization. By offering flexible funding models, phased sales, and dynamic pricing mechanisms, we ensure fair access, price stability, and sustainable token economies.

---

## Token Sale Smart Contract

This project contains a **Move module** defining a flexible and extensible token sale flow on the **SUI blockchain**.

### Current Functionality

The **Token Sale Smart Contract** provides features for managing multi-stage sales with dynamic pricing, customizable stages, and reserve token handling.

#### 1. **Manual Stage Initialization**

The `init_stage` function allows the admin to **manually initialize** each sale stage. This provides flexibility for both admin control and automation via frontend systems.

**⚙️ Current Behavior**:

- The **first stage is not auto-initialized** during `launch_sale`.
- Each new stage requires explicit initialization.
- Admin or frontend can trigger the stage initialization.

**Future Enhancements**:

- Automatic triggers based on token sales or when the current stage reaches its `end_date`.

**Frontend Integration**:

- Can be automated with **React `useEffect`** to track countdowns and trigger `init_stage` once the current stage ends.

#### 2. **Dynamic Pricing Strategy**

The price for each stage can be **dynamically calculated** based on the mode passed. The pricing can be:

- **Fixed Step**: Increment per stage.
- **Burn Scaled**: Adjusted based on burned tokens.
- **Sold Ratio Scaled**: Adjusted based on how much of the hard cap has been sold.

**Planned Upgrade**:

- The price calculation will be enhanced with a **`price_mode`** field, enabling fully dynamic pricing behavior, including automatically updating pricing modes based on parameters like tokens sold or time elapsed.

---

## Example Contract Functionality

Here's a brief overview of how pricing and stage management are structured in the contract:

### Stage Initialization

```move
public entry fun init_stage<T: drop>(
    sale: &mut Token_Sale<T>,
    admin_cap: &AdminCap,
    clock: &Clock,
    price_mode: pricing::PriceMode,
    ctx: &mut TxContext
) {
    let now = timestamp_ms(clock);
    let stage_number = vector::length(&sale.stages);

    if (stage_number > 0) {
        let last = vector::borrow(&sale.stages, stage_number - 1);
        let allowed_time = last.end_date;
        assert!(now >= allowed_time, 1302); // STAGE_TOO_EARLY
    };

    let amount = tokens_per_stage(sale);
    let tokens = coin::mint(&mut sale.treasury_cap, amount, ctx);

    let duration = sale.stage_duration;
    let end = now + duration * MS_PER_DAY;

    let price = pricing::price_for_stage(
        sale.base_price,
        stage_number,
        0, // reserve
        sale.hard_cap,
        0, // total_sold
        &price_mode
    );

    let stage = Stage<T> {
        id: object::new(ctx),
        stage_number,
        start_date: now,
        end_date: end,
        price,
        tokens_for_sale: tokens,
        reserve: 0,
        tokens_sold: 0,
        duration
    };

    vector::push_back(&mut sale.stages, stage);
}
```
