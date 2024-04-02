---
description: The tasks in this section describe how to set up a Sei validator
---

# Run a Sei Validator

## What is a Validator? <a href="#what-is-a-validator" id="what-is-a-validator"></a>

Validators are responsible for committing new blocks to the blockchain through an automated voting process. A validator's stake is slashed if they become unavailable or sign blocks at the same height.&#x20;

The following instructions assume you have already gone through the [Run a Sei Validator](./) and are synchronized to the latest block height.

Validators run full nodes, participate in consensus by broadcasting votes, commit new blocks to the blockchain, and participate in the governance of the blockchain. Validators can cast votes on behalf of their delegators. A validator's voting power is weighted according to their total stake. Only validators in the **Active Validator Set** are the only validators that sign blocks and receive revenue.

Validators and their delegators earn the following rewards:

* **Fees** are added to each transaction to avoid spamming and pay for computing power. Validators set minimum gas prices and reject transactions that have implied gas prices below this threshold.
* **Mints** periodically the chain will mint new tokens, this is configured in the [mint module](https://github.com/sei-protocol/sei-chain/tree/master/x/mint)

Validators can set commissions on the fees they receive as an additional incentive.

For fees and mints, the tokens are distributed in every block relative to the number of tokens that a validator has staked.&#x20;

If validators double sign, are frequently offline, or do not participate in governance, their staked Sei (including the Sei of users that delegated to them) can be slashed. Penalties can vary depending on the severity of the violation.



\
