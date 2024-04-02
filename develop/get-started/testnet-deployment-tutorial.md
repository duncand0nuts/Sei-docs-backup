---
description: Deploy a Cosmwasm smart contract to the public testnet
---

# Testnet deployment tutorial

### 1. Public Full-node Information

You can find available public RPC endpoints information according to chain ids under the [#public-rpcs](../resources.md#public-rpcs "mention") section.

### 2. Create Sei Account + Key

Create and configure your account:

```
export ACCOUNT_NAME="<YOUR_ACCOUNT_NAME_HERE>"
seid keys add $ACCOUNT_NAME
```

This should create a new account locally and display its address and mnemonic:

```
- name: $ACCOUNT_NAME
  type: local
  address: sei1ep9jyk9kydjz0fhadm7rzy6pc9ga7tdt4d26xn
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"Ai3obIQZXAmdgDSw7p1awFIPGK9b7cu/SfJM6nbc+hV/"}'
  mnemonic: ""


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.
some mnemonic words some mnemonic words some mnemonic words some mnemonic words some mnemonic words 
Store the shown account address into local env variable and add it as the genesis account to the chain:
```

```
export ACCOUNT_ADDRESS="<ADDRESS_OF_YOUR_ACCOUNT>"
```

### 3. Fund your account

Use the [faucet](../resources.md#sei-faucet) to fund your account. It may be possible that your account needs more funds, in which case please reach out to our team on Discord / Telegram.

Verify your balance on the testnet:

```
seid q bank balances $ACCOUNT_ADDRESS --node $RPC_ENDPOINT --chain-id $CHAIN_ID
```

### 4. Upload & Instantiate Wasm Contract

Store a compiled wasm binary to the sei network:

```
seid tx wasm store $CONTRACT_WASM_BINARY -y --from=$ACCOUNT_NAME --chain-id=$CHAIN_ID --node $ENDPOINT --gas=10000000 --fees=100000usei --broadcast-mode=block
```

Once your proposal is stored to the testnet, you can instantiate your contract:

```shell
seid tx wasm instantiate $CONTRACT_ID '{}' --chain-id sei-chain --from $ACCOUNT_NAME --gas=4000000 --fees=1000000usei --broadcast-mode=block --label $LABEL --no-admin
```

Note that the `'{}'` part is the parameters you pass to instantiate the contract. In this example, the contract takes no parameter so `'{}'` suffices. For any real world contracts, their instantiation parameters would likely be non-empty.&#x20;

You should get a response like the following:

```yaml
height: "2051"
info: ""
logs:
- events:
  - attributes:
    - key: _contract_address
      value: sei1wug8sewp6cedgkmrmvhl3lf3tulagm9hnvy8p0rppz9yjw0g4wtqdxfzff
    - key: code_id
      value: "1"
    type: instantiate
  - attributes:
    - key: action
      value: /cosmwasm.wasm.v1.MsgInstantiateContract
    - key: module
      value: wasm
    - key: sender
      value: cosmos1ep9jyk9kydjz0fhadm7rzy6pc9ga7tdt4d26xn
    type: message
  log: ""
  msg_index: 0
raw_log: '[{"events":[{"type":"instantiate","attributes":[{"key":"_contract_address","value":"cosmos1wug8sewp6cedgkmrmvhl3lf3tulagm9hnvy8p0rppz9yjw0g4wtqdxfzff"},{"key":"code_id","value":"2"}]},{"type":"message","attributes":[{"key":"action","value":"/cosmwasm.wasm.v1.MsgInstantiateContract"},{"key":"module","value":"wasm"},{"key":"sender","value":"cosmos1ep9jyk9kydjz0fhadm7rzy6pc9ga7tdt4d26xn"}]}]}]'
timestamp: ""
tx: null
txhash: 3033A3673367169693157DF4D22D973A012C03A4FBE452E994E4DE87C8628D23
```

From the output, you can see that your contract was created at address: `sei1wug8sewp6cedgkmrmvhl3lf3tulagm9hnvy8p0rppz9yjw0g4wtqdxfzff`. You will need this address for any contract interaction.
