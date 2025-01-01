---
sidebar_position: 1
sidebar_label: Intro
---
# Governance at DAOlympus

:::warning

DAOlympus governance has *not* been deployed yet. The following docs are provided for informational purposes only and are subject to change.

:::

The DAOlympus protocol will be governed and upgraded by tokenholders using three components:

1. gDAOHM token (Governance DAOHM)
2. Governor Bravo
3. Multisig

Together, these components enable the community to propose, vote on, and implement changes to the DAOlympus V3 system. Proposals can modify system parameters, activate or deactivate policies, and install or upgrade modules, effectively allowing the addition of new features and the mutation of the protocol.

## gDAOHM
gDAOHM, or Governance OHM, is an ERC-20 token used for proposing upgrades to DAOlympus protocol. gDAOHM can be obtained by wrapping DAOHM, and vice versa. The only use cases of gOHM today is for voting.

### Delegation
gDAOHM allows the owner to delegate voting rights to any address, including their own. There's a few considerations to keep in mind when delegating:

* Users can delegate to only one address at a time
* The entire gDAOHM balance of the delegator is added to the delegatee’s vote count
* Changes to the delegator's token balance automatically adjust the voting rights of the delegatee
* Votes are delegated from the current block and onward, until the delegator delegates again or transfers all their gDAOHM

Delegation can be achieved by calling the `delegate()` function or via a valid off-chain signature using `delegateBySig()`. DAOlympus will provide a frontend for managing delegations.
 

## Governor Bravo
DAOlympus implements a modified version of Compound’s Governor Bravo with the following key changes:

1. **Percent-based submission threshold** - The minimum amount of votes required by the proposer to submit the proposal. Set to 0.017% of the total gDAOHM supply, at time of proposal submission. Checked again during proposal queueing and execution.
2. **Percent-based quorum threshold** - The minimum amount of FOR votes required for a proposal to qualify to pass. Set to 20% of the gDAOHM supply at time of proposal activation.
3. **Net votes approval threshold** - OCG introduces the concept of net votes, which is the margin of votes between FOR and NO in order for a proposal to qualify to pass. Specifically, the percentage of **voting supply** voting FOR must be 60%, or greater.

The decision to introduce these changes is followed from the original OlympusDAO protocol and stems from elasticity in the gDAOHM supply. Percent-based thresholds ensure that requirements (in absolute gDAOHM terms) for proposing and executing proposals scale/shrink with the token supply.


### Parameters
| Variable | Description | Value |
|-|-|-|
| proposalThreshold | % of total supply required in order for a voter to become a proposer |  0.017% of supply |
| quorumPct | minimum % of total supply voting FOR in order for a proposal to qualify to pass | 20% |
| highRiskQuorum | Same as quorumPct but specific to a high risk module in the Default system | 20% |
| approvalThresholdPct | minimum % of voting supply voting FOR in order for a proposal to qualify to pass | 60% |
| proposalMaxOperations | The maximum number of actions that can be included in a proposal | 15 actions |
| votingDelay | The delay before voting on a proposal may take place, once proposed, in blocks | 3 days |
| votingPeriod | The duration of voting on a proposal, in blocks | 7 days |
| activationGracePeriod |The amount of time once a proposal is eligible for activation that it can be activated before considered expired | 1 day |
| GRACE_PERIOD | How long after a proposal is eligible for execution it can still be executed before it is considered expired | 1 day |
| delay (execution delay) | The time a proposal must be queued for before it can be executed | 1 day |
| vetoGuardian | Address which has veto power over all proposals | Emergency MS |
| MIN_GDAOHM_SUPPLY   | The minimum level of gDAOHM supply acceptable for OCG operations | 1000 gDAOHM |


## Shared Roles
Multisigs may perform protocol upgrades for roles that are not yet fully under Governor Bravo's Timelock control. The multisigs may queue and execute on-chain actions that are approved by the community through [Snapshot](https://docs.snapshot.org/), an off-chain governance client. Today, the following roles are under shared Timelock and multisig control:

| Role | Responsibility | Systems affected | Multisig |
|-------- | -------- | -------------- | -------------- |
| `callback_admin` | Callback to interface with Bond system | RBS | DAO MS + Timelock |
| `heart_admin` | Manages heartbeats | RBS and Staking | DAO MS + Timelock |
| `custodian` | Treasury custodian that can approve, remove assets from TRSY | TRSRY | DAO MS + Timelock |
| `emergency_restart` | Restart MINTR, TRSRY | All systems | DAO MS + Timelock |
| `emergency_admin` | Emergency shutdown for BLV | All systems | Emergency MS + Timelock |
| `emergency_shutdown` | Shutdown MINTR, TRSRY | All systems | Emergency MS + Timelock |


## Multisig
Multisigs perform protocol upgrades for roles that are not yet under Governor Bravo's Timelock control. The multisigs queue and execute on-chain actions that are approved by the community through [Snapshot](https://docs.snapshot.org/), an off-chain governance client. Today, the following roles are under multisig control:


| Role | Responsibility | Systems affected | Multisig |
|-------- | -------- | -------------- | -------------- |
| `executor` | Single address permission: Ability to install modules and policies on Kernel | Kernel | DAO MS |
| `operator_operate` | Triggers heartbeat RBS updates | RBS | DAO MS |
| `operator_reporter` | Records a bond purchase and updates capacity accordingly. Limited to the `BondCallback` contract. | RBS | DAO MS
| `bondmanager_admin` | Create and manage new bond markets | OHM and other non-RBS managed bonds | DAO MS |

