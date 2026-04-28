---
title: "Contract Development"
description: "Setup, build, test, and deploy DOMAN smart contracts with Foundry"
---

# Contract Development

---

## Prerequisites

- [Foundry](https://book.getfoundry.sh/) (forge, cast, anvil)
- Node.js (for frontend integration)

---

## Setup

```shell
git clone <repo-url>
cd doman-contracts
forge install
```

Create a `.env` file:

```env
PRIVATE_KEY=your_private_key
BASE_RPC_URL=https://sepolia.base.org
```

---

## Development Workflow

```mermaid
graph LR
    Write["Write Solidity<br/>src/ScamReporter.sol"]
    Build["forge build"]
    Test["forge test -vv"]
    Snapshot["forge snapshot"]
    Deploy["forge script<br/>--broadcast"]

    Write --> Build --> Test --> Snapshot --> Deploy
```

---

## Build

```shell
forge build
```

---

## Test

```shell
forge test -vv
```

---

## Deploy

```shell
forge script script/ScamReporter.s.sol:ScamReporterScript \
  --rpc-url base-sepolia \
  --private-key $PRIVATE_KEY \
  --broadcast
```

### Deployment Flow

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant Foundry as Foundry CLI
    participant RPC as Base Sepolia RPC
    participant Chain as Base Sepolia

    Dev->>Foundry: forge script --broadcast
    Foundry->>Foundry: Compile & simulate
    Foundry->>RPC: Send deployment tx
    RPC->>Chain: Include in block
    Chain-->>RPC: Contract address + tx hash
    RPC-->>Foundry: Receipt
    Foundry->>Foundry: Save to broadcast/
    Foundry-->>Dev: Deployed at 0x6553...237a
```

---

## Gas Snapshot

```shell
forge snapshot
```

---

## Foundry CLI Reference

```shell
$ forge --help
$ anvil --help
$ cast --help
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Smart Contracts | Solidity ^0.8.13 |
| Framework | Foundry |
| Blockchain | Base Chain (Sepolia testnet) |
| Off-chain DB | PostgreSQL (Supabase) |
| Indexer | Ponder |
| Frontend | Next.js 16, React 19, Wagmi v3, Viem |

---

## Architecture Overview

```mermaid
graph TD
    subgraph Foundry["Foundry Project"]
        Src["src/<br/>ScamReporter.sol"]
        Test["test/<br/>ScamReporter.t.sol"]
        Script["script/<br/>ScamReporter.s.sol"]
    end

    subgraph Deploy["Deployment Target"]
        BaseSepolia["Base Sepolia<br/>Chain ID: 84532"]
    end

    subgraph Consumers["Consumers"]
        Frontend["DOMAN Frontend<br/>(Wagmi / Viem)"]
        Indexer["Ponder Indexer"]
    end

    Script -->|"forge script --broadcast"| BaseSepolia
    Src --> BaseSepolia
    BaseSepolia -->|"Events"| Indexer
    Frontend -->|"submitVote()"| BaseSepolia
```
