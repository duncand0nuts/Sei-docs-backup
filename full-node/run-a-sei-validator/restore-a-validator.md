# Restore a Validator

## Restore a validator

A validator can be completely restored on a new Sei node with the following set of keys:

* The Consensus key, stored in `~/.sei/config/priv_validator.json`
* The mnemonic to the validator wallet

{% hint style="danger" %}
Before proceeding, ensure that the existing validator is not active. Double voting has severe slashing consequences.
{% endhint %}

To restore a validator:

1. Setup a full Sei node synced up to the latest block.
2. Replace the `~/.sei/config/priv_validator.json` file of the new node with the associated file from the old node, then restart your node.
