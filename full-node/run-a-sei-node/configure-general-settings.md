# Configure General Settings

The following information describes the most important node configuration settings found in the `~/.sei/config/` directory. It is recommended that you update these settings with your own information.

<details>

<summary>Structure of <code>~/.sei/config</code></summary>

```
~/.sei/config
│-- app.toml                            # seid configuration file
│-- client.toml                         # configurations for the cli wallet (ex seid)
│-- config.toml                         # Tendermint configuration file
│-- genesis.json                        # gensesis transactions
│-- node_key.json                       # private key used for node authentication in the p2p protocol (its corresponding public key is the nodeid)
└-- priv_validator_key.json             # key used by the validator on the node to sign blocks
```

</details>

### Genesis File <a href="#initialize-and-configure-moniker" id="initialize-and-configure-moniker"></a>

To get the Genesis file for a certain network, please see [Resources](../../develop/resources.md)&#x20;

### Initialize and configure the moniker[​](https://docs.terra.money/full-node/run-a-full-terra-node/configure-general-settings#initialize-and-configure-moniker) <a href="#initialize-and-configure-moniker" id="initialize-and-configure-moniker"></a>

A Moniker is the custom username of your node, it should be human-readable. It's set at the time of node setup and can be used to provide more descriptive or friendly names to identify nodes, as opposed to using IP addresses or public key hashes which can be hard to remember or recognize.

1. Set your custom moniker&#x20;

```bash
export MONIKER="YOUR_MONIKER"
```

2. Initialize the node&#x20;

```bash
seid init $MONIKER --chain-id <Network> -o
```

### Different types of peers in `~/.sei/config/config.toml`

```
# Comma-separated list of peers to be added to the peer store
# on startup. Either BootstrapPeers or PersistentPeers are
# needed for peer discovery
bootstrap-peers = ""

# Comma-separated list of nodes to keep persistent connections to
persistent-peers = ""

# List of node IDs, to which a connection will be (re)established ignoring any existing limits
unconditional-peer-ids = ""
```

### Set up `external-address` in `config.toml`[​](https://docs.terra.money/full-node/run-a-full-terra-node/configure-general-settings#set-up-external\_address-in-configtoml)

If your public-facing address is different from the internal address that you're using you need to configure `external-address` it in `config.toml`. This addition will prevent continuous reconnections. The default p2p-port is `26656`(See  [Run a Sei Node](./)).

```bash
sed -i -e 's/external-address = \"\"/external_address = \"'$(curl httpbin.org/ip | jq -r .origin)':26656\"/g' ~/.sei/config/config.toml
```
