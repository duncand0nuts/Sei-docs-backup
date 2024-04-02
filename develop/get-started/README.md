---
description: Use this beginner-friendly guide to start building on Sei.
---

# Get started

{% hint style="info" %}
Are you an EVM developer? Visit the Sei V2 Devnet docs [here](https://v2.docs.sei.io/).
{% endhint %}

## 1. Install local dependencies

To get started running Sei, follow the [local dependency installation](local-dependencies.md) tutorial

## 2. Set up Seid

The `seid` command is a command-line tool to interact with the Sei blockchain.&#x20;

Follow the [Seid setup tutorial](seid.md) to get started using `seid`

## 3. Run a chain locally

Follow the [initialization script](../developer-tools/local-sei-node.md) to to run a cluster locally with a single validator node and 50 preconfigured wallet accounts.

## 4. Create and deploy a simple counter app

Follow the [counter app tutorial](counter-smart-contract-tutorial/) to write and deploy a simple Rust smart contract to your local version of Sei

{% hint style="warning" %}
Sei supports CosmWasm 1.0.0+ smart contracts. Older versions of CosmWasm will have errors getting deployed
{% endhint %}

## 5. Create a decentralized app frontend

Follow the [frontend tutorial](counter-smart-contract-tutorial/frontend-tutorial.md) to create a simple frontend for the counter app tutorial above

## 6. Create and deploy a simple exchange

Follow the [spot exchange tutorial](spot-exchange-tutorial.md) to write a simple Rust based spot exchange on Sei

## 7. Deploy to testnet

Follow the [testnet deployment tutorial](testnet-deployment-tutorial.md) to deploy the counter app to the atlantic-2 testnet, and update the frontend to interact with that\
