---
description: Optimizing Smart Contracts to Run in Parallel
---

# Parallelism

## Summary

In this section, developers can learn to make their smart contracts run concurrently. By making these optimizations by enabling parallel execution, smart contract transactions can run concurrently, allowing Sei to sustain greater levels of load and throughput.

## Introduction

Developers can optimize their smart contract performance on chain by enabling parallel execution for the contract.  The way we parallelize execution with sei is by defining resources that a contract may need access to, and these can then be used to create a parallelization plan for a block such that transactions that have mutually exclusive resource requirements can execute in parallel, and those with shared resources are appropriately ordered to preserve in-order access to the shared resource.

In order to incentivize developers to parallelize their smart contracts, there is an automatic 50% gas discount applied for contracts that have PROPERLY defined their resource requirements and dependencies.&#x20;

In order to parallelize a wasm contract's execution, the developer needs to go through the execute messages that your contract implements and identify other chain resources (executes or queries) that it needs access to as part of execution. This includes queries for chain native information such as oracle prices or bank balances and/or queries to other wasm contracts. Additionally, contract developers must include resources used by any execute messages that the contract may return for further execution. Whether it returns other wasm executes, or chain native messages such as bank sends, the contract developer must take those into account when defining resources used by the contract.

Once these resources are identified, the contract developer will then need to register them appropriately with the chain so that they can be referenced by the chain when parallelizing transactions in a block. This is done by introducing a json formatted proposal that specifies all of the resources that the contract may need access to. Below is an example of such a proposal json that can be used as a skeleton for developers to register their own contracts' dependencies.

<pre class="language-json"><code class="lang-json"><strong>{
</strong>    "wasm_dependency_mapping":
        {
          "contract_address": "{your-contract-address}",
          "base_access_ops": [
            // you can replace these with your own dependencies
            {
              "operation": {
                "access_type": "UNKNOWN",
                "resource_type": "ANY",
                "identifier_template": "*"
              },
              "selector_type": "NONE"
            },
            {
              "operation": {
                "access_type": "COMMIT",
                "resource_type": "ANY",
                "identifier_template": "*"
              },
              "selector_type": "NONE"
            }
          ]
        }
}
</code></pre>

When defining the dependencies as part of the proposal, you can base them off of the [parallelization guide](broken-reference) for different module messages and queries, and can customize them appropriately by using [Selectors](broken-reference).

Once this json file is built, the developer can register it with the chain with the tx below:

```bash
seid tx accesscontrol register-wasm-dependency-mapping $JSON_FILEPATH --from $YOUR_KEY --fees 2000usei -b block -y
```
