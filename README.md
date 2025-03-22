# BitAnalytics Protocol - Smart Contract Documentation

## Overview

BitAnalytics is a Bitcoin-native decentralized analytics protocol built on the Stacks blockchain. It enables secure, trustless participation in analytics data governance through a Proof-of-Stake (PoS) mechanism with tiered rewards and voting capabilities. This contract implements core protocol logic including staking, governance, and reward distribution.

## Key Features

- **Bitcoin-Native Analytics**: Leverages Stacks for Bitcoin settlement finality
- **Tiered Staking System**: 3 tiers with escalating privileges
- **Enhanced Reward Multipliers**: Up to 2x rewards through long-term commitments
- **On-Chain Governance**: Proposal creation and voting weighted by stake
- **Protocol Security**: Cooldown periods & emergency controls
- **Transparent Analytics**: Immutable record of data governance decisions

## Architecture

### Core Components

| Component          | Description                                    |
| ------------------ | ---------------------------------------------- |
| `ANALYTICS-TOKEN`  | Protocol utility token (fungible)              |
| `UserPositions`    | Tracks user stakes, rewards, and tier status   |
| `TierLevels`       | Configures requirements/benefits for each tier |
| `Proposals`        | Stores governance proposals and voting data    |
| `StakingPositions` | Manages active staking commitments             |

### System Parameters

| Parameter         | Value               | Description              |
| ----------------- | ------------------- | ------------------------ |
| Base Reward Rate  | 5%                  | Annual base yield        |
| Minimum Stake     | 1,000,000 µSTX      | 1 STX minimum stake      |
| Cooldown Period   | 1,440 blocks (~24h) | Unstaking waiting period |
| Proposal Duration | 100-2,880 blocks    | Custom voting periods    |

## Getting Started

### Prerequisites

- Stacks-compatible wallet (Hiro, Xverse)
- STX tokens for gas and staking
- Clarinet SDK (for local development)

### Contract Deployment

```bash
clarinet contract deploy bitanalytics
```

## Core Functionality

### Staking Operations

#### `stake-stx`

```clarity
(stake-stx amount lock-period)
```

Stakes STX tokens to participate in protocol governance and earn rewards.

**Parameters:**

- `amount`: Minimum 1 STX (1,000,000 µSTX)
- `lock-period`: 0 (flexible), 4,320 (30d), or 8,640 (60d) blocks

**Example:**

```clarity
(contract-call? .bitanalytics stake-stx u5000000 u8640)
```

### Unstaking Process

1. **Initiate Unstaking**

```clarity
(initiate-unstake amount)
```

Starts cooldown period for specified amount

2. **Complete Unstaking**

```clarity
(complete-unstake)
```

Withdraws funds after cooldown expires

### Governance System

#### Proposal Lifecycle

1. **Creation**

```clarity
(create-proposal "Upgrade reward system" u1440)
```

- Requires Tier 2+ status
- 1,000,000 µSTX voting power minimum

2. **Voting**

```clarity
(vote-on-proposal u42 true)
```

- Votes weighted by staked amount
- Voting during proposal window only

3. **Execution**

- Automatic execution when:
  - Quorum reached (1M votes)
  - Majority achieved
  - Execution period valid

## Reward System

### Calculation Formula

```
Rewards = (Staked Amount × Base Rate × Multiplier × Blocks Staked) / 14,400,000
```

Where:

- **Base Rate**: 5% (500 basis points)
- **Multiplier**: Tier × Lock Period bonus

### Tier Structure

| Tier | Minimum STX | Multiplier | Governance Rights |
| ---- | ----------- | ---------- | ----------------- |
| 1    | 1           | 1x         | Basic voting      |
| 2    | 5           | 1.5x       | Proposal creation |
| 3    | 10          | 2x         | Protocol upgrades |

## Security Features

### Protocol Safeguards

- **Cooldown Periods**: 24h unstaking delay
- **Emergency Mode**: Contract freeze capability
- **Input Validation**: Strict parameter checks
- **Owner Controls**: Privileged pause/resume functions

### Error Handling

| Code | Error            | Resolution         |
| ---- | ---------------- | ------------------ |
| 1000 | Unauthorized     | Verify permissions |
| 1001 | Invalid Protocol | Check parameters   |
| 1004 | Cooldown Active  | Wait 1,440 blocks  |
| 1006 | Below Minimum    | Stake ≥1 STX       |

### Audit Checklist

1. Validate reward calculations
2. Test tier transitions
3. Verify proposal execution logic
4. Stress test edge cases
5. Review access controls
