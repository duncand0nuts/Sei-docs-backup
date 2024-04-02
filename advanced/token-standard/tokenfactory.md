# Tokenfactory

This page gives examples of basic `tokenfactory` module `cli` commands to mint `sdk.Coins` on Sei.

For descriptions about each `tokenfactory` module message types visit the [documentation](https://github.com/sei-protocol/sei-chain/tree/master/x/tokenfactory).



The tokenfactory module allows any account to create a new token with the name `factory/${CREATOR_ADDRESS}/${SUBDENOM}` in a permissionless fashion.

See the tutorial at the end of this page for an example `sdk.Coin` creation using the tokenfactory module.

For more details on how to interact with tokenfactory module via `wasm` please reference the [`sei-cosmwasm` package](https://github.com/sei-protocol/sei-cosmwasm/tree/main/packages/sei-cosmwasm) and its associated `Tokenfactory` bindings.

### Creating a Denom

```
seid tx tokenfactory create-denom $SUBDENOM --from $ACCOUNT --chain-id $CHAIN_ID
```

This will create a new coin of the form `factory/${CREATOR_ADDRESS}/${SUBDENOM}`

### Minting

NOTE: Minting is only possible from the creator address account

```
seid tx tokenfactory mint $AMOUNT --from $ACCOUNT --chain-id $CHAIN_ID
```

`$AMOUNT` for example includes `100sei`&#x20;

Once minted, consider a `bank send` to send the tokens to another address

### Burning

NOTE: Burning is only possible from the creator address account

```
seid tx tokenfactory burn $AMOUNT --from $ACCOUNT --chain-id $CHAIN_ID
```

###

### Example Tutorial

In this Tutorial we will create a new `sdk.Coin` on `atlantic-2` testnet.&#x20;

We employ a test creator address `sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g` and walk through each step of the process

1. **Create the denom.**&#x20;

Here we specify a subdenom `solid`&#x20;

```
seid tx tokenfactory create-denom solid --from sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g --chain-id atlantic-2  -b block -y
```



2\. **Query module to verify creation.**

```
seid q tokenfactory denoms-from-creator sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g
denoms:
- factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid
```

We use the tokenfactory query `denoms-from-creator` to query all the tokenfactory created `sdk.Coins` associated with address.&#x20;

Notice how the the full denomination is `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid` . This verifies our address `sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g` as the creator who can mint and burn.&#x20;

When interacting with this `sdk.Coin` , we must **specify the full denomination** `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid`not just the subdenom `solid` .



3\. **Mint 1000** `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid`

```
seid tx tokenfactory mint 1000factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid --from sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g --chain-id atlantic-2 -b block -y
```

This will mint 1000 `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid` to the creator address.

**NOTE:** Consider a **bank send** after minting to distribute to other addresses.



4\. **Query bank balance of creator address to verify mint**

```
seid q bank balances sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g
balances:
- amount: "1000"
  denom: factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid
```

You can see how the 1000 `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid` shows up in the creator address' bank balance.&#x20;



5\. **Burn 500** `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid`

```
seid tx tokenfactory burn 500factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid --from sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g --chain-id atlantic-2 -b block -y
```



6\. **Query Bank balances of creator to verify burn**

```
seid q bank balances sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g
balances:
- amount: "500"
  denom: factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid
```

You can see now how there is only 500 `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid` left in the creator address.



7\. **Bank send 100** `factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid`t**o another address** `sei1fsjz3adpv3v4zlu6dwn4qm8n98r8ard2jma7eu`

```
seid tx bank send sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g sei1fsjz3adpv3v4zlu6dwn4qm8n98r8ard2jma7eu 100factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid --chain-id=atlantic-2 -b block -y
```



8\. **Query bank balance of receiver address to verify bank send**

```
seid q bank balances sei1fsjz3adpv3v4zlu6dwn4qm8n98r8ard2jma7eu
balances:
- amount: "100"
  denom: factory/sei1cux78sqj8nk8g0j4e39wwrntzqye2fcpmwvr8g/solid
```
