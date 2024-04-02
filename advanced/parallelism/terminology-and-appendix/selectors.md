# Selectors

Selectors are used to make dependencies more specific to the resource they will utilize. This enables a greater level of wasm contract parallelization and is suggested to tailor the wasm contract's dependencies to the use case. Below, we will give an overview of each selector type along with an examples of where it might be used when defining wasm dependencies. When using a selector, developers will need to specify a `selector type` as well as a `selector value` that indicates how to customize the dependency.

{% hint style="warning" %}
**NOTE**: The examples provided are not suggestions on how to implement smart contracts, and maybe using some antipatterns in their description. They are simply for a case example of how a specific Selector may be used.
{% endhint %}

## Selectors

### NONE

This selector type `None` is used to indicate that no selector functionality is needed. This can be used when the resource identifier is a constant or is `*` indicating blanket access for a given resource type.

#### Example

Lets say we have a wasm contract which needs to check the bank balances of all of its users to verify their qualification to continue using the contract (lets say "whale status"). In this case, we may have stored a list of users of the contract, and every time the contract is called, it reverifies the list by checking the user's bank balances. In this case, since we wouldn't know exactly what users / addresses need their bank balances checked, we would need to read many bank balances, and for that reason, we would use the identifier `*` along with the resource type `KV_BANK_BALANCES`. Because there is not additional granularity to be achieved, we do not want to utilize selectors here, so we can set the `selector_type` as `NONE` to disable selectors for this dependency.

```json
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "*"
    },
    "selector_type": "NONE"
}
```

### JQ\_BECH32\_ADDRESS

This selector can be used to extract an address from a wasm execute message to use it as part of a defined dependency. The selector value for this selector type acts similar to the indexing pattern used to access json attributes using the `jq` tool.

#### Example

Lets say we have a wasm contract that allows a user to send funds to another user with execute message `fancy_send_funds`, but we need to verify that the other account exists prior to send the funds. We can query for a users account that was passed into an example execute message such as:

```json
{
    "fancy_send_funds": {
        "recipient_addr": "sei1234foobar..."
    }
}
```

Then, we could define a dependency for that users account with the following dependency:

```json
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_AUTH_ADDRESS_STORE",
        "identifier_template": "01%s"
    },
    "selector_type": "JQ_BECH32_ADDRESS",
    "selector": ".fancy_send_funds.recipient_addr"
},
```

Note how we specify the retrieval pattern for the user's address using `".fancy_send_funds.recipient_addr"`. This indicates the dependency generator to populate the identifier with the value in the `fancy_send_funds` object's attribute called `recipient_addr`.

### JQ\_LENGTH\_PREFIXED\_ADDRESS

This one is quite similar to the previous one ([JQ\_BECH32\_ADDRESS](selectors.md#jq\_bech32\_address)), but with the caveat that it generates the address along with a length prefix, a pattern that is used for some specific message types such as Bank Send.

#### Example

Imagine a scenario similar to the one above, but we also need to check user balances as part of the fund transfer, with an execute message:

```json
{
    "fancy_send_funds": {
        "recipient_addr": "sei1234foobar..."
    }
}
```

Then, we could define a dependency for that user's bank balance with the following dependency:

```json
{
    "operation": {
        "access_type": "READ",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "02%s"
    },
    "selector_type": "JQ_LENGTH_PREFIXED_ADDRESS",
    "selector": ".fancy_send_funds.recipient_addr"
},
```

### SENDER\_BECH32\_ADDRESS

Similar to the JQ pattern discussed previously, in many cases, the sender of the execute message to the contract will need to be referenced for generating contract dependencies. As such, we expose selector types that will use the sender address as the input for the dependency generation. For this selector type, no selector value is needed since the dependency generator will properly use the sender's address.

#### Example

Lets say we have a wasm contract that allows a user to withdraw their funds from the contract with some function `withdraw_funds`, and as part of the bank send that occurs in that wasm message execution, we need to read the user's account, which necessitates the following dependency:

```json
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

### SENDER\_LENGTH\_PREFIXED\_ADDRESS

Similar to the previous item ([SENDER\_BECH32\_ADDRESS](selectors.md#sender\_bech32\_address)), this can be used to generate a sender address with a length prefix, meant for use for certain message types such as bank sends. For this selector type, no selector value is needed since the dependency generator will properly use the sender's address.

#### Example

Lets say we have a wasm contract that allows a user to withdraw their funds from the contract with some function `withdraw_funds`, and as part of the bank send that occurs in that wasm message execution, we need to write the user's bank balance, which necessitates the following dependency:

```json
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "02%s"
    },
    "selector_type": "SENDER_LENGTH_PREFIXED_ADDRESS",
    "selector": "NONE"
},
```

### CONTRACT\_ADDRESS

This selector type is used for building dependencies that involve a contract itself, such as defining resource access for wasm store (where the contract stores its state). This can be used for defining dependencies for your own contract, or for external contract dependencies that your contract may need access to as part of contract execution. With this selector type, the selector value is the bech32 prefixed (sei...) contract address.

#### Example

As part of defining a wasm contract's dependencies, it needs to define proper write access to the wasm store where the contract state is stored. For that, we would need to define a dependency like the following:

```json
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_WASM_CONTRACT_STORE",
        "identifier_template": "03%s"
    },
    "selector_type": "CONTRACT_ADDRESS",
    "selector": "sei123contractaddressfoobar"
},
```

### JQ\_MESSAGE\_CONDITIONAL

This selector type is used as a conditional case to determine when a certain "heavy" dependency access may be necessary. This may involve `*` access on a resource type for a specific / rare execute message. The selector value is the JQ pattern match value for the specific execute message for which the dependency is to be generated.

#### Example

Lets take a contract used for moving escrowed funds between users. In the majority of cases, the dependencies would only involve one or two users. However, lets take a rarer execute message called `process_all_user_transfers` that would be infrequently called, but would transfer escrowed funds waiting to be disbursed to users as a bulk of bank sends. In this case, we would need widespread write access to bank balances which would be defined by `*` as the identifier, but we can limit this dependency being used to ONLY that execute message, allowing all other contract executions to be more parallelized. The dependency we might define with this selector type would look like:

```json
{
    "operation": {
        "access_type": "WRITE",
        "resource_type": "KV_BANK_BALANCES",
        "identifier_template": "*"
    },
    "selector_type": "JQ_MESSAGE_CONDITIONAL",
    "selector": ".process_all_user_transfers"
},
```
