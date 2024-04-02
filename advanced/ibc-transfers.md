# IBC Transfers

NOTE: For an in-depth explanation on fungible ibc token transfers, please reference [IBC Docs](https://github.com/cosmos/ibc/blob/main/spec/app/ics-020-fungible-token-transfer/README.md)



This page gives examples and an overview of `ibc` with `Sei`, to send assets between multiple `ibc`-compatible chains.

NOTE: In order to send `ibc` transfers to another chain, there must be a `ibc` channel and relayers set up with that chain. To setup a relayer, reference [`hermes` relayer documentation](https://hermes.informal.systems/) or the [`ibc-go` github](https://github.com/cosmos/ibc-go).

Run following in order to list out all available ibc channels

```
seid q ibc channel channels
```

NOTE: Beforehand, follow instructions to install the `seid` command line tool from the `sei-chain` repo.



### IBC Transfers

First verify token balances:

```
seid q bank balances $ACCOUNT_ADDRESS --chain-id=$CHAIN_ID
```

Submit a ibc token transfer:

```
seid tx ibc-transfer transfer $SRC_PORT $SRC_CHANNEL $RECEIVER_ADDRESS $AMOUNT --chain-id=$CHAIN_ID --from=$ACCOUNT --fees=$FEE -y
```

NOTE: the `seid tx ibc-transfer transfer --help` contains useful information about the `cli` tool for submitting ibc transfers&#x20;

Example: Sending 1000usei from `atlantic-2` (sei incentivized testnet) to `deweb` chain using the `transfer` source port, `channel-135` source channel

```
seid tx ibc-transfer transfer transfer channel-135 $DEWEB_WALLET 1000usei --chain-id=atlantic-2 --from=$ACCOUNT --fees=300usei -y
```
