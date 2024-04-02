---
description: Install the Seid binary for interacting with a Sei chain
---

# Seid

## Prerequisites

This guide assumes you have the dependencies installed already, see [Local Dependencies](local-dependencies.md)

## Setup

The `seid` command is a command-line tool to interact with the Sei blockchain.&#x20;

To install `seid`, find the right seid version you want to use [here](https://github.com/sei-protocol/sei-chain/releases), and then run the following commands&#x20;

```bash
git clone https://github.com/sei-protocol/sei-chain
cd sei-chain
git checkout $RELEASE
make install
```

You can verify that `seid` was installed properly by running

```bash
$ seid version
3.3.3
```

{% hint style="info" %}
If you see an error like `command not found: seid` then you may need to set your GOPATH environment variable.\
\
1\. Use `go env GOPATH` to find your GOPATH\
2\. add &#x20;

```
export GOPATH=[your GOPATH from step 1]
export PATH=$PATH:$GOPATH/bin
```

to your \~/.bashrc\
\
For more information [see here. ](https://pkg.go.dev/cmd/go#hdr-GOPATH\_environment\_variable)
{% endhint %}

`seid` supports general Cosmos SDK and Tendermint commands. You can run the tool to see a list of commands with explanations of what they do:

```
Start sei app

Usage:
  seid [command]

Available Commands:
  add-genesis-account      Add a genesis account to genesis.json
  add-wasm-genesis-message Wasm genesis subcommands
  collect-gentxs           Collect genesis txs and output a genesis.json file
  config                   Create or query an application CLI configuration file
  debug                    Tool for helping with debugging your application
  export                   Export state to JSON
  gentx                    Generate a genesis tx carrying a self delegation
  help                     Help about any command
  init                     Initialize private validator, p2p, genesis, and application configuration files
  keys                     Manage your application's keys
  migrate                  Migrate genesis to a specified target version
  query                    Querying subcommands
  rollback                 rollback cosmos-sdk and tendermint state by one height
  start                    Run the full node
  status                   Query remote node for status
  tendermint               Tendermint subcommands
  tx                       Transactions subcommands
  validate-genesis         validates the genesis file at the default location or at the location passed as an arg
  version                  Print the application binary version information

Flags:
  -h, --help                help for seid
      --home string         directory for config and data (default "/root/.sei")
      --log_format string   The logging format (json|plain) (default "plain")
      --log_level string    The logging level (trace|debug|info|warn|error|fatal|panic) (default "info")
      --trace               print out full stack trace on errors

Use "seid [command] --help" for more information about a command.
```
