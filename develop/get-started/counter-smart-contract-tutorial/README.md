---
description: Create and deploy a simple Cosmwasm smart contract locally
---

# Counter smart contract tutorial

{% hint style="warning" %}
Before proceeding, please complete [steps 1 to 3 from the get started guide](../).&#x20;
{% endhint %}

## CosmWasm

CosmWasm is a library for writing Rust smart contracts. Sei supports CosmWasm 1.0.0+ smart contracts.

## Dependencies

Following the instructions in the [CosmWasm github](https://github.com/CosmWasm/cw-template) to install cargo-generate and cargo-run-script

## Create a boilerplate smart contract

Go to a folder in which you want to create a boilerplate smart contract and run

```bash
cargo generate --git https://github.com/CosmWasm/cw-template.git --branch 1.0 --name counter -d minimal=false
```

{% hint style="warning" %}
If you encounter an error saying `` `no such command: generate` `` please make sure you have `cargo-generate` installed

```rust
cargo install cargo-generate
```
{% endhint %}

This will create a repository with the following structure

```
.
├── Cargo.toml
├── LICENSE
├── NOTICE
├── README.md
└── src
    ├── bin
    │   └── schema.rs
    ├── contract.rs
    ├── error.rs
    ├── helpers.rs
    ├── lib.rs
    ├── msg.rs
    └── state.rs
```

This contract defines a simple counter app. It supports 4 basic functions:&#x20;

* Instantiate the counter with a starting count
* Increment the counter by 1
* Reset the counter
* Read the current count

To run unit tests, you can run

```
cargo unit-test
```

which will give an output similar to the following

```
running 4 tests
test contract::tests::increment ... ok
test contract::tests::reset ... ok
test contract::tests::proper_initialization ... ok
test integration_tests::tests::count::count ... ok

test result: ok. 4 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

## Deploy boilerplate smart contract

To deploy a contract, you will first need to compile the contract. Confirm that the prerequisites listed in [the CosmWasm docs ](https://github.com/CosmWasm/cw-template/blob/main/Developing.md)are met, and then run the following

```bash
cargo wasm
```

Before uploading, use the rust-optimizer to minimize the size of the binary that will be uploaded - you will need to use the correct version for your processor architecture.

**x86 64-bits (Intel/AMD):**

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.14.0
```

**ARM 64-bits (M1/M2 Macs):**

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer-arm64:0.14.0
```

Then create a new file called **deploy.sh**

```
.
├── Cargo.lock
├── Cargo.toml
├── Developing.md
├── Importing.md
├── LICENSE
├── NOTICE
├── Publishing.md
├── README.md
├── artifacts
│   ├── checksums.txt
│   ├── checksums_intermediate.txt
│   └── counter.wasm
├── deploy.sh
├── src
│   ├── bin
│   │   └── schema.rs
│   ├── contract.rs
│   ├── error.rs
│   ├── helpers.rs
│   ├── integration_tests.rs
│   ├── lib.rs
│   ├── msg.rs
│   └── state.rs
└── target
    ├── ...
```

Paste the following code into **deploy.sh**

```bash
if [ -z "${contract}" ];
then contract=artifacts/counter.wasm
fi 
if [ -z "${keyname}" ];
then keyname=admin
fi 
if [ -z "${password}" ];
then password="12345678\n"
fi 

seid=~/go/bin/seid
code=$(printf $password | $seid tx wasm store $contract -y --from=$keyname --chain-id=sei-chain --gas=10000000 --fees=10000000usei --broadcast-mode=block | grep -A 1 "code_id" | sed -n 's/.*value: "//p' | sed -n 's/"//p')
printf "Code id is %s\n" $code
admin_addr=$(printf $password |$seid keys show $keyname | grep -A 1 "address" | sed -n 's/.*address: //p')
printf "Admin addr id is %s\n" $admin_addr
addr=$(printf $password |$seid tx wasm instantiate $code '{"count": 0}' --from $keyname --broadcast-mode=block --label "counter" --chain-id sei-chain --gas=30000000 --fees=3000000usei --admin=$admin_addr -y | grep -A 1 -m 1 "key: _contract_address" | sed -n 's/.*value: //p' | xargs)
printf "Deployed counter address is %s\n" $addr
```

{% hint style="warning" %}
NB: if you are using an ARM processor (M1/M2 Mac) then you will need to change the contract name to **`counter-aarch64.wasm`** in line 2 of the deploy script above
{% endhint %}

The output will be as follows

```
$ ./deploy.sh 
Code id is 1
Admin addr id is sei1tn08ycqyk8wy7v72n0zz9l6nvk46rgj3aqmewj
Deployed counter address is sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m
```

## Increment count

Next we want to increment the counter and confirm that the state is updated

Create a new file called **increment.sh** with the following

```bash
if [ -z "${keyname}" ];
then keyname=admin
fi 
if [ -z "${password}" ];
then password="12345678\n"
fi 

seid=~/go/bin/seid
contract='' # Fill in contract address from deployment step, ex. sei14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sh9m79m

increment_resp=$(printf $password |$seid tx wasm execute $contract '{"increment":{}}' --from $keyname --broadcast-mode=block  --chain-id sei-chain --gas=30000000 --fees=3000000usei -y )

new_count=$($seid q wasm contract-state smart $contract '{"get_count":{}}' | grep -A 1 "count:" | awk -F: '/count:/{getline; print $2}')
printf "New count: %s\n" "$new_count"
```

and then run the script twice

```bash
$ ./increment.sh 
New count:  1
$ ./increment.sh 
New count:  2
```
