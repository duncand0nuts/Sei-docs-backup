# Bank Module

## Messages

Below are some of the message types that parallelization is supported for, and the dependencies that need to be specified if using the message with your wasm contract.

### Bank Send

{% hint style="warning" %}
**Note**: these dependencies are necessary if your contract sends funds to users, **OR** if it receives funds from users.
{% endhint %}

```json
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_BANK_BALANCES",
    "identifier_template": "0220%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
{
  "operation": {
    "access_type": "WRITE",
    "resource_type": "KV_BANK_BALANCES",
    "identifier_template": "0220%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_AUTH_ADDRESS_STORE",
    "identifier_template": "01%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
```

We will also need to add dependencies that specify the other party in the bank send. For this, we can specify the address using the selectors, and below we show the dependencies that would need to be used in order to perform a bank send from the wasm contract to the address that called the contract (aka the `sender`).

```json
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_BANK_BALANCES",
    "identifier_template": "02%s"
  },
  "selector_type": "SENDER_LENGTH_PREFIXED_ADDRESS",
  "selector": "NONE"
},
{
  "operation": {
    "access_type": "WRITE",
    "resource_type": "KV_BANK_BALANCES",
    "identifier_template": "02%s"
  },
  "selector_type": "SENDER_LENGTH_PREFIXED_ADDRESS",
  "selector": "NONE"
},
{
  "operation": {
    "access_type": "WRITE",
    "resource_type": "KV_AUTH_ADDRESS_STORE",
    "identifier_template": "01%s"
  },
  "selector_type": "SENDER_BECH32_ADDRESS",
  "selector": "NONE"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_AUTH_ADDRESS_STORE",
    "identifier_template": "01%s"
  },
  "selector_type": "SENDER_BECH32_ADDRESS",
  "selector": "NONE"
},
```

The dependencies in this section are subject to change based on the [selectors](../terminology-and-appendix/selectors.md) used to determine what account addresses are involved in the bank send.
