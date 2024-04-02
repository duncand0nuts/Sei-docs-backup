# Tokenfactory Module

## Messages

### Create Denom

{% hint style="info" %}
**Note**: These Create Denom dependencies can be used as-is, there shouldn't be any necessary changes related to selectors for this message type
{% endhint %}

```json
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_TOKENFACTORY",
        "identifier_template": "*"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_TOKENFACTORY",
        "identifier_template": "*"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_DENOM",
        "identifier_template": "01666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_DENOM",
        "identifier_template": "01666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_SUPPLY",
        "identifier_template": "*"
    },
    "selector_type": "NONE"
},
```

### Mint

```json
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "0220%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "{contract-address}"
},
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "0220%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "{contract-address}"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_DENOM",
        "identifier_template": "01666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_SUPPLY",
        "identifier_template": "00666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_SUPPLY",
        "identifier_template": "00666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_AUTH_ADDRESS_STORE",
        "identifier_template": "012e6443cca08670b1a6b0ceda12e0369a7ff6be5a"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_TOKENFACTORY_DENOM",
        "identifier_template": "%s"
    },
    "selector_type": "CONSTANT_STRING_TO_HEX",
    "selector": "denoms|factory/{contract-address}"
},
```

## Burn

```json
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_SUPPLY",
        "identifier_template": "00666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_SUPPLY",
        "identifier_template": "00666163746f7279"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "0220%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "{contract-address}"
},
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "0220%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "{contract-address}"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_TOKENFACTORY_DENOM",
        "identifier_template": "%s"
    },
    "selector_type": "CONSTANT_STRING_TO_HEX",
    "selector": "denoms|factory/{contract-address}"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_AUTH_ADDRESS_STORE",
        "identifier_template": "012e6443cca08670b1a6b0ceda12e0369a7ff6be5a"
    },
    "selector_type": "NONE"
},
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_AUTH_ADDRESS_STORE",
        "identifier_template": "01%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "{contract-address}"
},
```
