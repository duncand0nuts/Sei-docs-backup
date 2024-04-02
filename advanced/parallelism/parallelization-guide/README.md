# Parallelization Guide

The content below will guide wasm contract developers on what access operations to include in their wasm dependencies based on what messages and queries they use.

## Steps to Register your Wasm Contract

1. Deploy and instantiate your Wasm Contract to Sei blockchain
2. Create a dependency mapping json file according to the [Dependency Mapping Template](./#dependency-mapping-template)
3. Add [Wasm Contract Base Dependencies](./#wasm-contract-base-dependencies) to the dependency json
4. Follow the [Module Specific Dependencies](./#module-specific-dependencies) section to add extensive dependencies
5. Follow [Integration and Debugging](../integration-and-debugging.md) steps to verify the dependency json before deploying

## Dependency Mapping Template

Below is a template that you can use to register dependency mapping to your contract:

```json
{
  "wasm_dependency_mapping": {
    "contract_address": "{your-wasn-contract-address}",
    "base_access_ops": [
      // add access operations here    
    ]
  }
}
```

Make sure to replace the _contract\_address_ placeholder with the correct address, and add basic [Wasm Contract Base Dependencies](./#wasm-contract-base-dependencies) and extensive [Module Specific Dependencies](./#module-specific-dependencies) if needed.

## Wasm Contract Base Dependencies

There are certain base dependencies required for wasm contract parallelization, and are outlined below. For these, you'll need your **wasm contract address** (sei....) and also the **code ID of the wasm contract** (with the value represented as a hex prefixed by zeroes with a length of 16). This code ID is obtained from the response when initially storing the code for your contract.

Example:&#x20;

Code ID 2: `0000000000000002`

Code ID 47: `000000000000002F`

```json
{
  "operation": {
    "access_type": "WRITE",
    "resource_type": "KV_WASM_CONTRACT_STORE",
    "identifier_template": "03%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CONTRACT_STORE",
    "identifier_template": "03%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CODE",
    "identifier_template": "01{code-id-hex-prefixed-with-zeros-with-length-16}"
  },
  "selector_type": "NONE"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_PINNED_CODE_INDEX",
    "identifier_template": "07{code-id-hex-prefixed-with-zeros-with-length-16}"
  },
  "selector_type": "NONE"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CONTRACT_ADDRESS",
    "identifier_template": "02%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-wasm-contract-address}"
},
```

{% hint style="warning" %}
Make sure you provide the correct _code-id-hex-prefixed-with-zeros-with-length-16_ and _your-wasm-contract-address_
{% endhint %}

Additionally, at the end of the dependencies specified for the wasm contract, you'll need to add a COMMIT access operation:

```json
{
  "operation": {
    "access_type": "COMMIT",
    "resource_type": "ANY",
    "identifier_template": "*"
  },
  "selector_type": "NONE"
}
```

## Wasm Message Specific Dependencies

The different execution paths of your smart contract may have different resource usages, and to parallelize in the most optimal way, it is possible to define resource dependencies that are specific to the message being sent to the contract. Using this approach, the base access ops can remain simpler and cover the common resource usages, and these message specific dependencies can be used to declare resources for certain messages that may be more computationally expensive or use many resources.

The format for the message specific dependencies is the exact same as for base dependencies, with the caveat being that they are only appended to the contract dependency if/when that specific message is called for the contract.

### Execute Message Dependencies

The execute message dependencies can be used to map these message specific dependencies to the execute message that utilizes those resources. They can be added in a section called `execute_access_ops` added along with `base_access_ops` in the json mapping file.

When the contract is called with an execute message that has these message specific dependencies, the specific dependencies are appended to the `base_access_ops` to construct the holistic contract dependencies.

Example:

```json
{
  "wasm_dependency_mapping": {
    "contract_address": "{your-wasn-contract-address}",
    "base_access_ops": [
      // add access operations here    
    ],
    "execute_access_ops": [
      {
        "message_name": "{execute-message-name}",
        "wasm_operations": [
          // Add access operations here (specific to the execute message)
        ]
      },
      {
        "message_name": "{other-execute-message-name}",
        "wasm_operations": [
          // Add access operations here (specific to the execute message)
        ]
      }
    ]
  }
}
```

### Query Message Dependencies

The query message dependencies can be used to map these message specific dependencies to the query message that utilizes those resources. They can be added in a section called `query_access_ops` added along with `base_access_ops` in the json mapping file.

When the contract is called with a query message that has these message specific dependencies, the specific dependencies are appended to the `base_access_ops` to construct the holistic contract dependencies.

These are more relevant for other contracts that may want to query your smart contract, since they would utilize the dependencies specified by this contract for proper parallelization.

Example:

```json
{
  "wasm_dependency_mapping": {
    "contract_address": "{your-wasn-contract-address}",
    "base_access_ops": [
      // add access operations here    
    ],
    "query_access_ops": [
      {
        "message_name": "{query-message-name}",
        "wasm_operations": [
          // Add access operations here (specific to the execute message)
        ]
      },
      {
        "message_name": "{other-query-message-name}",
        "wasm_operations": [
          // Add access operations here (specific to the execute message)
        ]
      }
    ]
  }
}
```

## Module Specific Dependencies

A Wasm contract may `query` or `call` (using `SubMsg` in the response) other modules of the Sei blockchain. These requests represents READ/WRITE invocations to the corresponding the resources and need to be defined in your dependency mapping. In this section, we will provide guidance for how to write access operation for different types of request. Below are the subpages for dependencies needed for each module based on the functionality used from that module by the wasm contract.

<table><thead><tr><th width="278">Module </th><th>Request Types</th></tr></thead><tbody><tr><td><h3><a href="bank-module.md"><strong>Bank Module</strong></a></h3></td><td><a href="bank-module.md#bank-send">Bank Send</a></td></tr><tr><td><h3><a href="tokenfactory-module.md"><strong>Tokenfactory Module</strong></a></h3></td><td><a href="tokenfactory-module.md#create-denom">Create Denom</a><br><a href="tokenfactory-module.md#mint">Mint</a><br><a href="tokenfactory-module.md#burn">Burn</a></td></tr></tbody></table>
