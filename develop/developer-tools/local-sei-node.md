---
description: >-
  In this guide, we'll walk you through how to set up the Sei blockchain locally
  on your machine.
---

# Local Sei Node

### Running a Local Single-node Testnet

To run Sei locally, run the following command

```bash
./scripts/initialize_local_chain.sh
```

Once you run the initialization script, the seid process will be running 1 node locally. It will also seed 50 accounts.&#x20;

To verify the status of the local blockchain, open a new tab and run

```bash
seid status | jq
```

If the chain is running properly, you will see an output similar to the following

```json
{
  "NodeInfo": {
    "protocol_version": {
      "p2p": "8",
      "block": "11",
      "app": "0"
    },
    "id": "36126cf4875862c3388f04dcc636fc1557791dd7",
    "listen_addr": "tcp://0.0.0.0:26656",
    "network": "sei-chain",
    "version": "0.34.19",
    "channels": "40202122233038606100",
    "moniker": "demo",
    "other": {
      "tx_index": "on",
      "rpc_address": "tcp://127.0.0.1:26657"
    }
  },
  "SyncInfo": {
    "latest_block_hash": "0A708E540CC04445B3C5585ED2757FADCAD18FB8E2A403655B3DC90D0F588D49",
    "latest_app_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
    "latest_block_height": "1",
    "latest_block_time": "2022-09-04T17:59:07.314228Z",
    "earliest_block_hash": "0A708E540CC04445B3C5585ED2757FADCAD18FB8E2A403655B3DC90D0F588D49",
    "earliest_app_hash": "E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855",
    "earliest_block_height": "1",
    "earliest_block_time": "2022-09-04T17:59:07.314228Z",
    "catching_up": false
  },
  "ValidatorInfo": {
    "Address": "13A8F763B396AF5B835A10748C4EFEDB0F99AC28",
    "PubKey": {
      "type": "tendermint/PubKeyEd25519",
      "value": "7ztvoNO/8wxIkqTcsDQ3CLgCyF5yOz6WBqf0yGrmeuE="
    },
    "VotingPower": "70000000000000"
  }
}
```

If you want to run more than one nodes, please check the docker based approach in the [next page](docker-cluster.md).
