---
description: Use this guide to install Seid and connect to Sei network
---

# Build Seid

## Prerequisites&#x20;

* Ensure that you have followed [Local Dependencies](broken-reference) and installed all the dependencies required&#x20;
* Make sure you meet the hardware requirements [System Configuration](system-configuration.md)

## Public Endpoints

Please find more accurate public endpoints in [this page](../../develop/resources.md#public-rpcs).

<table><thead><tr><th width="259">Network</th><th>Version Endpoint</th></tr></thead><tbody><tr><td>atlantic-2 (Polkachu)</td><td><a href="https://sei-testnet-rpc.polkachu.com/abci_info">https://sei-testnet-rpc.polkachu.com/abci_info</a></td></tr><tr><td>atlantic-2 (Brochain)</td><td><a href="https://sei-testnet-2-rpc.brocha.in/abci_info">https://sei-testnet-2-rpc.brocha.in/abci_info</a></td></tr></tbody></table>

## Install Seid&#x20;

Find the latest version here from the [official Github](https://github.com/sei-protocol/sei-chain/releases) or from one of the version endpoints in the [Public Endpoints](build-seid.md#public-endpoints) section supported by validators. Follow [Seid](../../develop/get-started/seid.md) to install the latest version of Seid that's supported by the network that you want to join.

```
export ENDPOINT=$VERSION_ENDPOINT
export RELEASE=$(curl $ENDPOINT| jq ".response.version" -r)
git clone https://github.com/sei-protocol/sei-chain
cd sei-chain
git checkout $RELEASE
make install-all
```



