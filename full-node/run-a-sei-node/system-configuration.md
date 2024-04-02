---
description: Requirements
---

# System Configuration

{% hint style="info" %}
These are the minimum requirements for running a Sei node. Due to the nature of the chain being extremely fast, you may see your node fall behind otherwise.&#x20;
{% endhint %}

| Network    | CPU Cores             | RAM   | Disk      |
| ---------- | --------------------- | ----- | --------- |
| atlantic-2 | 16 Cores / 16 threads | 64 GB | 1 TB NVMe |

### Commonly used ports[​](https://docs.terra.money/full-node/run-a-full-terra-node/system-config#commonly-used-ports) <a href="#commonly-used-ports" id="commonly-used-ports"></a>

Seid uses the following TCP ports. Toggle their settings to match your environment.

* `26656`: The default port for the P2P protocol. This port is used to communicate with other nodes and must be open to join a network. However, it does not have to be open to the public. For validator nodes, configuring `persistent_peers` and closing this port to the public are recommended.

Additional ports:

* `1317`: The default port for interacting with the Seid API server for HTTP RESTful requests. This allows applications and services to interact with the Seid instance through RPC.&#x20;
* `26660`: The default port for interacting with the [Prometheus](https://prometheus.io/) database, which can be used to monitor the environment. In the default configuration, this port is not open.
* `26657`: The default port for the RPC protocol. Because this port is used for querying and sending transactions, it must be open for serving queries from Seid.&#x20;

These ports are all customizable in `~/.sei/config/config.toml` and `~/.sei/config/app/toml` discussed in the later sections along with other fields.
