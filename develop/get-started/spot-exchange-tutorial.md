---
description: Create and deploy a simple exchange smart contract locally
---

# Spot exchange tutorial

## Spot-lite Contract

Clone the following example spot-lite repo: [https://github.com/sei-protocol/spot-lite](https://github.com/sei-protocol/spot-lite)

## Dependencies

Following the instructions in the [CosmWasm github](https://github.com/CosmWasm/cw-template) to install cargo-generate and cargo-run-script

## Build and Deploy Spot-lite

To deploy a contract, you will first need to compile the contract. Run the following in the `spot-lite` folder:

```bash
cargo build
```

Run the following command that will compile and run the rust-optimizer to minimize the size of the binary that will be uploaded:

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.11
```

After running the optimization, you should be able to find a _spot\_lite.wasm_ file under the `artifacts` folder.

Then let's create a new file called **deploy.sh** and add following to it (replace as you need):

```bash
#/bin/bash

if [ -z "${contract}" ];
then 
	contract=artifacts/spot_lite.wasm
fi

if [ -z "${keyname}" ];
then 
	keyname=admin
fi

if [ -z "${password}" ];
then 
	password="12345678\n"
fi

if [ -z "${chain_id}" ];
then
	chain_id="sei-chain"
fi

seid=~/go/bin/seid

# deploy the contract 
code=$(printf $password | $seid tx wasm store $contract -y --from=$keyname --chain-id=$chain_id --gas=10000000 --fees=10000000usei --broadcast-mode=block | grep -A 1 "code_id" | sed -n 's/.*value: "//p' | sed -n 's/"//p')
admin_addr=$(printf $password |$seid keys show $keyname | grep -A 1 "address" | sed -n 's/.*address: //p')

# instantiate the contract
addr=$(printf $password |$seid tx wasm instantiate $code "{}" --from $keyname --broadcast-mode=block --label "spot" --no-admin --chain-id $chain_id --gas=30000000 --fees=300000usei -y | grep -A 1 -m 1 "key: _contract_address" | sed -n 's/.*value: //p' | xargs)
printf "Deployed contract code %d with spot lite contract address: %s\n" $code $addr
		
```

Run the above script on the sei chain node (validator or RPC node). You can set up a local chain by following the instructions [here](../developer-tools/).&#x20;

## Register Contract with Dex Module

Next we will register the contract with the dex module and register pairs to it. Create a new file called **register\_contract.sh** and add following to it:

```bash
#/bin/bash

if [ -z "${keyname}" ];
then 
	keyname=admin
fi

if [ -z "${password}" ];
then 
	password="12345678\n"
fi

if [ -z "${chain_id}" ];
then
	chain_id="sei-chain"
fi

# Register contract with Dex module, code comes from the result of the previous step
printf $password |$seid tx dex register-contract $addr $code false true 1000000000 -y --from=$keyname --chain-id=$chain_id --fees=10000000usei --gas=10000000 --broadcast-mode=block

# Register Pairs on the contract
register_pair_json=$(printf '{"batch_contract_pair":[{"contract_addr":"%s","pairs":[{"price_denom":"usei","asset_denom":"uatom","price_tick_size":"0.000000100000000000","quantity_tick_size":"0.000000100000000000"}]}]}\n' "$addr")
echo "$register_pair_json" > register_pair_tx.json
cat register_pair_tx.json |jq
printf $password |$seid tx dex register-pairs register_pair_tx.json -y --from=$keyname --chain-id=$chain_id --fees=10000000usei --gas=500000 --broadcast-mode=block

# Query to verify registered pairs
$seid q dex get-registered-pairs $addr
```

## Submit Orders

Finally we will submit test orders against the deployed contract. Create a file **test\_order.sh** and add the following:

```bash
#/bin/bash

if [ -z "${keyname}" ];
then 
	keyname=admin
fi

if [ -z "${password}" ];
then 
	password="12345678\n"
fi

if [ -z "${chain_id}" ];
then
	chain_id="sei-chain"
fi

# Submit orders
printf $password |$seid tx dex place-orders $addr 'LONG?1.01?5?usei?uatom?LIMIT?{"leverage":"1","position_effect":"Open"}' --amount=1000000000usei -y --from=$keyname --chain-id=$chain_id --fees=1000000usei --gas=50000000 --broadcast-mode=block
$seid q dex list-long-book $addr usei uatom
```

The long book for the spot lite contract orders will be shown.&#x20;
