# Validator FAQ

## General concepts[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#general-concepts) <a href="#general-concepts" id="general-concepts"></a>

#### What is a validator?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-a-validator) <a href="#what-is-a-validator" id="what-is-a-validator"></a>

Validators run full nodes, participate in consensus by broadcasting votes, commit new blocks to the blockchain, and participate in the governance of the blockchain. Validators are able to cast votes on behalf of their delegators. A validator's voting power is weighted according to their total stake.

#### What is a full node?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-a-full-node) <a href="#what-is-a-full-node" id="what-is-a-full-node"></a>

A full node is a program that validates the transactions and blocks of a blockchain. Validators must run full nodes. Full nodes require more resources than light nodes, which only process block headers and a small subset of transactions. Running a full node means you are running a non-compromised and up-to-date version of the Sei with low network latency and no downtime.

It is possible and encouraged for any user to run full nodes even if they do not plan to be validators.

#### What is staking?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-staking) <a href="#what-is-staking" id="what-is-staking"></a>

_**Staking**_ occurs when Sei holders delegate their Sei to a validator. Staking increases a validator's weight, which improves the likelihood of being selected to validate blocks, and in return, delegators get rewarded.

The Sei blockchain is a public Proof of Stake (PoS) blockchain. This means a validator's weight (total stake) is determined by the amount of staking tokens (Sei) they delegate to themselves plus the Sei bonded to them by external delegators. The weight of a validator determines whether or not they are an active validator and how frequently they can propose a block. Validators with a higher weight will propose blocks more frequently, and in turn, generate more revenue.

The active validator set is made up of the top validators who hold the most Sei. The barrier for entry into the network is dictated by the value of the lowest stake held in the validator set. For instance, if a validator is created with a higher stake than the bottom validator, then this newly created validator may join the active set. If validators double-sign or are frequently offline, they risk their staked Sei, including Sei delegated by users, by being slashed by the protocol to penalize negligence and misbehavior.

#### What is a delegator?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-a-delegator) <a href="#what-is-a-delegator" id="what-is-a-delegator"></a>

Delegators are Sei holders who want to receive staking rewards without the responsibility of running a validator. Through Station, a user can delegate Sei to a validator and in exchange receive a part of a validator's revenue.&#x20;

Delegators share the benefits and rewards of staking with their Validator. If a Validator is successful, its delegators will consistently share in the rewards structure. If a Validator is slashed, the delegator’s stake will also be slashed. This is why delegators should perform due-diligence on validators before delegating. Delegators can also diversify by spreading their stake over multiple validators.

Delegators play a critical role in the system, as they are responsible for selecting the validators to which they stake. Being a delegator is not a passive role. Delegators should remain vigilant, actively monitor the actions of their validators, and re-delegate whenever they feel their current validator does not meet their needs.

### Becoming a Validator[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#becoming-a-validator) <a href="#becoming-a-validator" id="becoming-a-validator"></a>

#### How do I become a validator?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#how-do-i-become-a-validator) <a href="#how-do-i-become-a-validator" id="how-do-i-become-a-validator"></a>

Any participant in the network can signal their intent to become a validator by creating a validator and registering its validator profile. The candidate then broadcasts a `create-validator` transaction, which contains the following data:

* **PubKey:** Validator operators can have different accounts for validating and holding liquid funds. The submitted PubKey must be associated with the private key the validator will use to sign _prevotes_ and _precommits_.
* **Address:** A `seivaloper-` address which is used to publicly identify your validator. The private key associated with this address is used to bond, unbond, and claim rewards.
* **Name** (also known as the **moniker**)
* **Website** _(optional but recommended)_
* **Description** _(optional but recommended)_
* **Initial commission rate:** The commission rate on block provisions, block rewards, and fees charged to delegators.
* **Maximum commission:** The maximum commission rate which the validator will be allowed to charge. (This cannot be changed)
* **Commission change rate**: The maximum daily increase of the validator's commission.(This cannot be changed)
* **Minimum self-bond amount**: The minimum amount of bonded Sei the validator needs at all times. If the validator's self-bonded stake falls below this limit, its entire staking pool will be unbonded.
* **Initial self-bond amount**: The initial amount of Sei the validator self-bonds.\


Once a validator is created and registered, Sei holders can delegate Sei to them, effectively adding stake to its pool. The total stake of a validator is the total of their self-bonded Sei plus the Sei bonded by external delegators.

### Validator keys and states[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#validator-keys-and-states) <a href="#validator-keys-and-states" id="validator-keys-and-states"></a>

#### What type of key do I need to use?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-type-of-key-do-i-need-to-use) <a href="#what-type-of-key-do-i-need-to-use" id="what-type-of-key-do-i-need-to-use"></a>

* **Consensus Keypair:** This Consensus Keypair is on the consensus layer and consists of a unique Private Key used to sign block hashes associated with a Public Key sei`valconspub`.
  * This Keypair is generated when a node is created with sei`d init`.
  * The Private Key can be found in the `priv_validator_key.json` file in the `config` directory after running sei`d init`.
  *   The Public Key is derived from the Private Key and can be found and seen by running the command sei`d tendermint show-validator`

      **Example:** sei`valconspub1zcjduc3qcyj09qc03elte23zwshdx92jm6ce88fgc90rtqhjx8v0608qh5ssp0w94c`

{% hint style="warning" %}
A validator requires the above key in order to identify itself on the network, sign blocks, and sign staking/operational transactions, such as voting on Governance proposals. It is the validator's sole responsibility to secure these keys and have a contingency backup plan in the event of contingencies.
{% endhint %}

#### What are the different states a validator can be in?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-different-states-a-validator-can-be-in) <a href="#what-are-the-different-states-a-validator-can-be-in" id="what-are-the-different-states-a-validator-can-be-in"></a>

After a validator is created with the `create-validator` transaction, it can be in three states:

* `bonded`: A validator that is in the active set and participates in consensus. This validator is earning rewards and can be slashed for misbehaviour.
* `unbonding`: A validator that is not in the active set and cannot participate in consensus. This validator is not earning rewards but can still be slashed for misbehaviour. This is a transition state from `bonded` to `unbonded`. If a validator does not send a `rebond` transaction while in `unbonding` mode, it will take three weeks for the state transition to complete.
* `unbonded`: A validator that is not in the active set and not signing blocks. Unbonded validators can't be slashed and can't earn any rewards from their operation. It is still possible to delegate Sei to unbonded validators. Undelegating from an `unbonded` validator is processed immediately.

All Delegators have the same state as their validator.

Delegations are not necessarily bonded. Sei can be delegated and bonded, delegated and unbonding, delegated and unbonded, or liquid.

#### What is "self-bonding"? How can I increase my "self-bond"?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-self-bonding-how-can-i-increase-my-self-bond) <a href="#what-is-self-bonding-how-can-i-increase-my-self-bond" id="what-is-self-bonding-how-can-i-increase-my-self-bond"></a>

A validator operator's "self-bond" refers to the amount of Sei delegated to itself. You can increase your self-bond by delegating more Sei to your validator account.

#### Can I delegate to a validator outside of the active set?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#can-i-delegate-to-a-validator-outside-of-the-active-set) <a href="#can-i-delegate-to-a-validator-outside-of-the-active-set" id="can-i-delegate-to-a-validator-outside-of-the-active-set"></a>

You can still delegate to a validator even if they do not appear on Station. Simply add the seivaloper address of your desired validator to the end of the following URL:

You can ask your validator for their seivaloper address.

Be careful when delegating to validators outside of the active set. Some inactive validators may be jailed or are no longer supported. Station only displays active or recently active validators who are able to participate in consensus.

#### Is there a faucet?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#is-there-a-faucet) <a href="#is-there-a-faucet" id="is-there-a-faucet"></a>

Use the [Sei faucet](https://app.seinetwork.io/faucet) to join testnet and devnets

#### Is there a minimum amount of Sei that must be staked to be an active (bonded) validator?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#is-there-a-minimum-amount-of-luna-that-must-be-staked-to-be-an-active-bonded-validator) <a href="#is-there-a-minimum-amount-of-luna-that-must-be-staked-to-be-an-active-bonded-validator" id="is-there-a-minimum-amount-of-luna-that-must-be-staked-to-be-an-active-bonded-validator"></a>

There is no set minimum. The top validators with the highest total stake (where total stake = self-bonded stake + delegated stake) make up the active validator set. The validator with the lowest stake among the top sets the barrier to entry for the active set.

#### How will delegators choose their validators?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#how-will-delegators-choose-their-validators) <a href="#how-will-delegators-choose-their-validators" id="how-will-delegators-choose-their-validators"></a>

Delegators are free to choose validators according to their own criteria. This may include:

* **Amount of self-bonded Sei:** The amount of Sei a validator self-bonds to its staking pool. A validator with a higher amount of self-bonded Sei has more skin in the game, making it more liable for its actions.
* **Amount of delegated Sei:** The total amount of Sei delegated to a validator. A high stake shows that the community trusts this validator; however, this also means that a validator is a bigger target for hackers. Large stakes provide large voting power. This weakens the network. At any given time, if 33% or more of staked Sei becomes inaccessible, the network will halt. Through incentives and education, this weakness can be prevented by delegating away from validators that have too much voting power. Validators sometimes become less attractive as their amount of delegated Sei grows.
  * Max voting power per validator is capped at 15%&#x20;
* **Commission rate:** The commission applied to rewards by a validator before being distributed to its delegators.
* **Track record:** Delegators can look at the track record of a validator they plan to delegate to. This includes seniority, past votes on proposals, historical average uptime, and how often the node was compromised.

Validators can also provide a website address for advertisement and to increase transparency. However, building a good reputation in the community will always be most important when attempting to attract delegators. It's also good practice for validators to have their setup audited by a third party. Please note that the Sei team will not approve or conduct any audits.

### Responsibilities[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#responsibilities) <a href="#responsibilities" id="responsibilities"></a>

#### Do validators need to be publicly identified?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#do-validators-need-to-be-publicly-identified) <a href="#do-validators-need-to-be-publicly-identified" id="do-validators-need-to-be-publicly-identified"></a>

No, they do not. Each delegator will value validators based on their own criteria. Validators are typically advised to register a website address when they nominate themselves so they can advertise their operation as they see fit.

#### What are the responsibilities of a validator?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-responsibilities-of-a-validator) <a href="#what-are-the-responsibilities-of-a-validator" id="what-are-the-responsibilities-of-a-validator"></a>

A validator must:

* **Run the correct software versions:** Validators need to make sure that their servers are always online, and that their private keys are not compromised.
* **Provide oversight and feedback on the correct deployment of community pool funds:** The Sei protocol includes a governance system for proposals to facilitate the adoption of its currencies. Validators are expected to hold budget executors to provide transparency and to use funds efficiently.
* **Be active members of the community:** Validators should always be up-to-date with the current state of the ecosystem so that they can easily adapt to any change.

#### What does staking imply?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-does-staking-imply) <a href="#what-does-staking-imply" id="what-does-staking-imply"></a>

Think of staked Sei as a safety deposit on a validator's activities. When a validator or a delegator wants to retrieve part or all of their deposit, they send an unbonding transaction. The staked Sei then undergoes a _three-week unbonding period_, during which it is vulnerable to slashing risks for potential misbehaviour committed by the validator before the start of the unbonding process.

Validators receive block provisions, block rewards, and fee rewards and share these with their delegators. If a validator misbehaves, a certain portion of their total stake is slashed (the severity of the penalty depends on the type of misbehaviour). This means that every user that bonds Sei to a slashed validator gets penalized in proportion to their stake. Delegators are incentivized to delegate to validators that function safely.

#### Can a validator run away with a delegators' Sei?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#can-a-validator-run-away-with-a-delegators-luna) <a href="#can-a-validator-run-away-with-a-delegators-luna" id="can-a-validator-run-away-with-a-delegators-luna"></a>

**No.** By delegating to a validator, users delegate staking power. The more staking power a validator has, the more weight it has in the consensus and general processes. This does not mean that the validator has custody of its delegators' Sei.

{% hint style="info" %}
You should always prioritize validators who you trust when staking for reasons such as slashing. However, you do not need to worry about your delegated funds being stolen as it is impossible for a validator to steal staked funds.
{% endhint %}

Although delegated funds cannot be stolen by validators, delegators are still liable if a validator misbehaves. When this happens, a delegator's stake will be partially slashed in proportion to their relative stake.

#### How often will a validator be chosen to propose the next block? Does it go up with the quantity of Sei staked?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#how-often-will-a-validator-be-chosen-to-propose-the-next-block-does-it-go-up-with-the-quantity-of-luna-staked) <a href="#how-often-will-a-validator-be-chosen-to-propose-the-next-block-does-it-go-up-with-the-quantity-of-lu" id="how-often-will-a-validator-be-chosen-to-propose-the-next-block-does-it-go-up-with-the-quantity-of-lu"></a>

The validator that is selected to mine the next block is called the **proposer**, or the "leader" in the consensus for the round. Each proposer is selected deterministically, and the frequency of being selected is equal to the relative total stake of the validator (Total stake = self-bonded stake + delegators stake). For example, if the total bonded stake across all validators is 100 Sei, and a validator's total stake is 10 Sei, then this validator will be chosen 10% of the time as the proposer.

### Incentives[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#incentives) <a href="#incentives" id="incentives"></a>

#### What are the incentives to stake?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-incentives-to-stake) <a href="#what-are-the-incentives-to-stake" id="what-are-the-incentives-to-stake"></a>

Each member of a validator's staking pool earns revenue:

* **Compute fees (gas)**: To prevent spamming, validators can set minimum gas fees for transactions to be included in their mempool. At the end of every block, compute fees are disbursed to the participating validators proportional to their stake.

This total revenue is divided among a validator's staking pool according to each validator's weight. The revenue is then divided among delegators in proportion to each delegator's stake. Note that a commission on delegators' revenue is applied by the validator before it is distributed.

#### What is the incentive to run a validator?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-the-incentive-to-run-a-validator) <a href="#what-is-the-incentive-to-run-a-validator" id="what-is-the-incentive-to-run-a-validator"></a>

Validators earn more revenue than their delegators through commission.

#### What is a validator's commission?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-is-a-validators-commission) <a href="#what-is-a-validators-commission" id="what-is-a-validators-commission"></a>

The revenue received by a validator's pool is split between a validator and their delegators. A validator can apply a commission on the part of the revenue that goes to its delegators. This commission is set as a percentage. Each validator is free to set its initial commission, maximum daily commission change rate, and maximum commission. The mainnet enforces the parameters that each validator sets. These parameters can only be defined when initially declaring candidacy, and may only be constrained further after being declared.

#### How are block provisions distributed?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#how-are-block-provisions-distributed) <a href="#how-are-block-provisions-distributed" id="how-are-block-provisions-distributed"></a>

Block provisions are distributed proportionally to each validator relative to their total stake. This means that even though each validator gains rewards with each provision, all validators will still maintain equal weight.

#### How are fees distributed?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#how-are-fees-distributed) <a href="#how-are-fees-distributed" id="how-are-fees-distributed"></a>

Fees are distributed to validators in the same way as commission: proportionally to each validator's stake relative to the total stake among all validators. A Block proposer can also get a bonus if it includes more than the minimum number of required precommits.

**Rewards**[**​**](https://docs.terra.money/full-node/manage-a-terra-validator/faq#rewards)

When a validator is selected to propose the next block, they must include at least two thirds of the precommits for the previous block in the form of validator signatures. Proposers who include more than two thirds receive a bonus proportional to the amount of additional precommits. This reward ranges from 1%, if the proposer includes two thirds of the precommits, to 5%, if the proposer includes 100% of the precommits. However, if a proposer waits too long, other validators may timeout and move on to the next proposer. This is why validators must find a balance between the amount of time spent waiting in order to receive a high proportion of signatures and the possible risk of losing out on proposing the next block. This feature aims to incentivize non-empty block proposals, improve networking between validators, and mitigate censorship.

#### What are the slashing conditions?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-slashing-conditions) <a href="#what-are-the-slashing-conditions" id="what-are-the-slashing-conditions"></a>

If a validator misbehaves, their bonded stake, along with their delegators' stake, will be slashed. The severity of punishment depends on the type of violation. The following are the main types of violations that can result in a slashing of funds:

* **Double-signing:** If someone reports on chain A that a validator signed two blocks at the same height on chain A and chain B, and if chain A and chain B share a common ancestor, then this validator will get slashed on chain A.
* **Unavailability:** If a validator's signature has not been included in the last X blocks, the validator will get slashed by a marginal amount proportional to X. If X is above a certain limit, then the validator will get unbonded.

{% hint style="danger" %}
Even if a validator does not intentionally misbehave, it can still be slashed if its node crashes, loses connectivity, gets DDoSed, or if its private key is compromised.
{% endhint %}

#### Are validators required to self-bond Sei?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#are-validators-required-to-self-bond-luna) <a href="#are-validators-required-to-self-bond-luna" id="are-validators-required-to-self-bond-luna"></a>

No, but self-bonding has benefits. A validator's total stake is made up of their self-bonded stake plus their delegated stake. This means that a validator can compensate for low amounts of self-bonded Sei by attracting more delegators. This is why reputation is very important for validators.

Although validators are not required to self-bond Sei, all validators should have `skin-in-the-game`. This can help make a validator more trustworthy.

In order for delegators to have some guarantee about how much `skin-in-the-game` their validator has, validators can signal a minimum amount of self-bonded Sei. If a validator's self-bond goes below the limit that it has predefined, this validator and all of its delegators will unbond.

### Technical Requirements[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#technical-requirements) <a href="#technical-requirements" id="technical-requirements"></a>

#### What are the hardware requirements?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-hardware-requirements) <a href="#what-are-the-hardware-requirements" id="what-are-the-hardware-requirements"></a>

See [system-configuration.md](../run-a-sei-node/system-configuration.md "mention") of the full node guide to learn the current minimum operating requirements.

#### What are the software requirements?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-software-requirements) <a href="#what-are-the-software-requirements" id="what-are-the-software-requirements"></a>

In addition to running a Sei node, validators should develop monitoring, alerting, and management solutions.

Validators should expect to perform regular software updates to accommodate upgrades and bug fixes. There will inevitably be issues with the network, and this requires vigilance.

#### What are the personnel requirements?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#what-are-the-personnel-requirements) <a href="#what-are-the-personnel-requirements" id="what-are-the-personnel-requirements"></a>

A successful validator will require the efforts of multiple highly skilled individuals and continuous attention to validator operations. In comparison, running a validator is considerably more involved than mining bitcoin.

Running an effective operation is critical to avoiding unexpected unbonding or being slashed. This includes being able to respond to attacks, outages, as well as to maintain security and isolation in your data center.

#### How can validators protect themselves from Denial-of-Service attacks?[​](https://docs.terra.money/full-node/manage-a-terra-validator/faq#how-can-validators-protect-themselves-from-denial-of-service-attacks) <a href="#how-can-validators-protect-themselves-from-denial-of-service-attacks" id="how-can-validators-protect-themselves-from-denial-of-service-attacks"></a>

Denial-of-service attacks occur when an attacker sends a flood of internet traffic to a validator's IP address. This can prevent a validator's server from connecting to the internet.

To do this, an attacker scans the network, attempts to retrieve the IP address of various validator nodes, and disconnects them by flooding them with traffic.

One way to mitigate these risks is to carefully structure a validator network with a sentry node architecture.

Validator nodes should only connect to full nodes that they trust. These nodes can be run by the same validator or other validators that they know. A validator node will typically run in a data center and most data centers provide direct links to major cloud providers. A validator can use these links to connect to sentry nodes in the cloud. This shifts the burden of denial-of-service from the validator's node directly to its sentry nodes. This may require new sentry nodes to be spun up or activated to mitigate attacks on existing ones.

Sentry nodes can be quickly spun up or used to change IP addresses. Because links to the sentry nodes are in private IP space, an internet based attack cannot directly disturb them. This will ensure a validator's block proposals and votes always make it to the rest of the network.

