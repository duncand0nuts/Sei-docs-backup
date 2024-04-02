---
description: Follow this guide to join an existing network through statesync
---

# Join a Network

To quickly stand up a fresh full node and join in the network, it's recommended to sync through **state sync.**

If you want to run a local instance of Sei, see [Local Sei Cluster](../../develop/developer-tools/local-sei-node.md)&#x20;

## Install Seid Binary

To stand up a sei node, the first step is to download and install seid on your machine.

The source code can be found in [https://github.com/sei-protocol/sei-chain.git](https://github.com/sei-protocol/sei-chain.git)

Make sure you checkout and install the same version as the chain you want to join.

```bash
export VERSION=v3.0.9
export CHAIN_ID="atlantic-2"
export MONIKER="replace-with-your-moniker-name"

git clone https://github.com/sei-protocol/sei-chain.git
git checkout $VERSION
cd sei-chain
make install
seid init --chain-id "$CHAIN_ID" "$MONIKER"
```

## State Sync

**State sync** allows a new node to join a network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. This can reduce the time needed to sync with the network from days to minutes.&#x20;

### Clean Up

If you are not starting a node from fresh, then you need to do some backups and clean ups.&#x20;

```bash
# Assuming your sei home directory is /root/.sei
# backup priv_validator_state.json
cp /root/.sei/data/priv_validator_state.json /root/priv_validator_state.json
# backup priv_validator_key.json
cp /root/.sei/config/priv_validator_key.json /root/priv_validator_key.json
# backup genesis.json
cp /root/.sei/config/genesis.json /root/genesis.json
rm -rf /root/.sei/data/*
rm -rf /root/.sei/wasm
rm -rf /root/.sei/config/priv_validator_key.json
rm -rf /root/.sei/config/genesis.json
```

Note: this step is not needed for fresh nodes

### Update Configurations

1. Set up rpc servers for primary and secondary endpoints. You can use one of the RPC endpoint as primary and secondary in [Resources](../../develop/resources.md#statesync-rpcs) page

```bash
# Example: Set polkachu rpc as rpc-servers
PRIMARY_ENDPOINT=https://sei-testnet-rpc.polkachu.com:443
sed -i.bak -e "s|^rpc-servers *=.*|rpc-servers = \"$PRIMARY_ENDPOINT,$PRIMARY_ENDPOINT\"|" ~/.sei/config/config.toml
```

2. Set up trust height and trust hash. Each snapshot is created at a certain block height, and best practice here is to set the trust height to be earlier than the latest snapshot block height to avoid backward verifications.

```bash
# Example: set trust height and hash to be the block height 10,000 earlier
PRIMARY_ENDPOINT=https://sei-testnet-rpc.polkachu.com:443
TRUST_HEIGHT_DELTA=10000
LATEST_HEIGHT=$(curl -s "$PRIMARY_ENDPOINT"/block | jq -r ".block.header.height")
if [[ "$LATEST_HEIGHT" -gt "$TRUST_HEIGHT_DELTA" ]]; then
  SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - $TRUST_HEIGHT_DELTA))
else
  SYNC_BLOCK_HEIGHT=$LATEST_HEIGHT
fi
# Get trust hash
SYNC_BLOCK_HASH=$(curl -s "$PRIMARY_ENDPOINT/block?height=$SYNC_BLOCK_HEIGHT" | jq -r ".block_id.hash")
# Override configs
sed -i.bak -e "s|^trust-height *=.*|trust-height = $SYNC_BLOCK_HEIGHT|" ~/.sei/config/config.toml
sed -i.bak -e "s|^trust-hash *=.*|trust-hash = \"$SYNC_BLOCK_HASH\"|" ~/.sei/config/config.toml
```

3. Set up persistent peers. If you need additional `bootstrap-peer` or `persistent-peer` (used for known good peers) in `~/.sei/config/config.toml.`See [Configure General Settings](configure-general-settings.md) for more info about each peer field.

```bash
# Example: Get the peers from polkachu rpc server
PRIMARY_ENDPOINT=https://sei-testnet-rpc.polkachu.com:443
SELF=$(cat /root/.sei/config/node_key.json |jq -r .id)
curl "$PRIMARY_ENDPOINT"/net_info |jq -r '.peers[] | .url' |sed -e 's#mconn://##'|grep -v "$SELF"  > PEERS
PERSISTENT_PEERS=$(paste -s -d ',' PEERS)
sed -i.bak -e "s|^persistent-peers *=.*|persistent-peers = \"$PERSISTENT_PEERS\"|" ~/.sei/config/config.toml
```

4. Enable state sync and disable db sync. Note, you can only enable either state sync or db sync at a time.

```bash
# Enable state sync
sed -i.bak -e "s|^enable *=.*|enable = true|" ~/.sei/config/config.toml
sed -i.bak -e "s|^db-sync-enable *=.*|db-sync-enable = false|" ~/.sei/config/config.toml
```

### Restore Backups&#x20;

Once you finished the above steps, if you previously have done the clean up step, we need to restore the previously backed up files.

```bash
# Restore previously backed up files
cp /root/priv_validator_state.json /root/.sei/data/priv_validator_state.json
cp /root/priv_validator_key.json /root/.sei/config/priv_validator_key.json
cp /root/genesis.json /root/.sei/config/genesis.json
```

