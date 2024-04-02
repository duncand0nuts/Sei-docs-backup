---
description: Define parallelization dependencies for a simple counter contract
---

# Parallelization Tutorial

{% hint style="warning" %}
Before continuing, please complete the steps in the [Counter Smart Contract](./) tutorial
{% endhint %}

## Initialize Parallelization Dependencies

We will continue from the counter contract directory created in the previous First, lets initialize the parallelization dependencies by creating a file called `parallelization.json` with the following boilerplate content (also can be found in [Parallelism](../../../advanced/parallelism/) section):

```json
{
  "wasm_dependency_mapping": {
    "contract_address": "{your-wasm-contract-address}",
    "base_access_ops": [
      // add access operations here    
    ]
  }
}
```

This sets up the template for the parallelization details to be added specifically for the contract.

## Define Contract Base Dependencies

Next, we want to define the dependencies that specify access to the contract's uploaded code and contract state. To do this, we can add the following to the `parallelization.json` file within the `base_access_ops` json array:

```json
// specifies a WRITE access for the contract state (mutating contract state)
{
  "operation": {
    "access_type": "WRITE",
    "resource_type": "KV_WASM_CONTRACT_STORE",
    "identifier_template": "03%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
// specifies a READ access for contract state (reading contract state)
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CONTRACT_STORE",
    "identifier_template": "03%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-contract-address}"
},
// specifies read access for the contract's metadata
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CONTRACT_ADDRESS",
    "identifier_template": "02%s"
  },
  "selector_type": "CONTRACT_ADDRESS",
  "selector": "{your-wasm-contract-address}"
},
// specifies the READ access for the uplaoded code (load the contract code for execution)
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CODE",
    "identifier_template": "01{code-id-hex-prefixed-with-zeros-with-length-16}"
  },
  "selector_type": "NONE"
},
// used to read whether the uploaded code is pinned
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_PINNED_CODE_INDEX",
    "identifier_template": "07{code-id-hex-prefixed-with-zeros-with-length-16}"
  },
  "selector_type": "NONE"
},
```

Note that you need to fill in the contract address for the selector where applicable to indicate the contract for which we need READ/WRITE permissions. Additionally, we need to add our uploaded code ID for READ access for the last two access operations. We should have the code ID from the previous tutorial when we uploaded the contract, first convert that to a hex value and prefix it with zeros to a length of 16.\
Example: `code id 1 -> 0000000000000001` or `code id 31 -> 000000000000001F`

When filling in the code ID, ensure that the prefix `01` or `07` is still present, such that the identifier templates for code ID `1` would be the following:

```json
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_CODE",
    "identifier_template": "010000000000000001"
  },
  "selector_type": "NONE"
},
{
  "operation": {
    "access_type": "READ",
    "resource_type": "KV_WASM_PINNED_CODE_INDEX",
    "identifier_template": "070000000000000001"
  },
  "selector_type": "NONE"
},
```

## Commit Access Operation

We also need to include a COMMIT access operation, which is necessary in any contract dependency mapping and it indicates the termination of the contract's dependencies. We can add the following access operation at the end of the ones added in the previous step:

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

## Register Dependency Mapping

Now, we want to register the dependencies with chain. We can do so by creating a bash script called `parallelization.sh` with the following content (note that the keyname must be the same as the admin used for contract instantiation):

```bash
if [ -z "${keyname}" ];
then keyname=admin
fi 
if [ -z "${password}" ];
then password="12345678\n"
fi 

seid=~/go/bin/seid

printf "Registering dependency mapping...\n"
printf $password | $seid tx accesscontrol register-wasm-dependency-mapping parallelism.json --from $keyname --broadcast-mode=block --chain-id sei-chain --fees=2000usei -y
```

We can run it with `./parallelism.sh` and then verify that it was registered using the following query:

```yaml
seid q accesscontrol wasm-dependency-mapping $contract_addr
# example output
wasm_dependency_mapping:
  base_access_ops:
  - operation:
      access_type: WRITE
      identifier_template: 03%s
      resource_type: KV_WASM_CONTRACT_STORE
    selector: sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m
    selector_type: CONTRACT_ADDRESS
  - operation:
      access_type: READ
      identifier_template: 03%s
      resource_type: KV_WASM_CONTRACT_STORE
    selector: sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m
    selector_type: CONTRACT_ADDRESS
  - operation:
      access_type: READ
      identifier_template: "010000000000000001"
      resource_type: KV_WASM_CODE
    selector: ""
    selector_type: NONE
  - operation:
      access_type: READ
      identifier_template: "070000000000000001"
      resource_type: KV_WASM_PINNED_CODE_INDEX
    selector: ""
    selector_type: NONE
  - operation:
      access_type: READ
      identifier_template: 02%s
      resource_type: KV_WASM_CONTRACT_ADDRESS
    selector: sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m
    selector_type: CONTRACT_ADDRESS
  - operation:
      access_type: COMMIT
      identifier_template: '*'
      resource_type: ANY
    selector: ""
    selector_type: NONE
  base_contract_references: []
  contract_address: sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m
  execute_access_ops: []
  execute_contract_references: []
  query_access_ops: []
  query_contract_references: []
  reset_reason: ""
```

## Verify Parallelism

In order to verify the proper functionality of these parallel dependencies, we can once again run the `increment.sh` script from the previous tutorial to perform executes and queries against the contract. After running this script, we can once again query for the wasm dependency mapping with the command from the previous step. If it still contains the same content, we know that there weren't any resource access violations detected as part of the contract execution.

In the event of invalid parallelization dependencies, the above query would instead return a generic dependency mapping such as the following:

```yaml
wasm_dependency_mapping:
  base_access_ops:
  - operation:
      access_type: UNKNOWN
      identifier_template: '*'
      resource_type: ANY
    selector: ""
    selector_type: NONE
  - operation:
      access_type: COMMIT
      identifier_template: '*'
      resource_type: ANY
    selector: ""
    selector_type: NONE
  base_contract_references: []
  contract_address: sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m
  execute_access_ops: []
  execute_contract_references: []
  query_access_ops: []
  query_contract_references: []
  reset_reason: incorrectly specified dependency access list
```



:tada::tada: Congrats :tada::tada: You have now parallelized your counter contract, allowing it to be executed concurrently with other transactions at the same time.
