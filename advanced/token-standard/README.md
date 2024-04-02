# Token Standard

## Introduction

Many Sei development standards focus on token interfaces. These standards help ensure smart contracts remain composable, so for instance when a new project issues a token, that it remains compatible with existing decentralized exchanges.

## Token Standard

Here are some of the most popular token standards on Sei:

* [**TokenFactory**](tokenfactory.md): This offers a standardized mechanism for creating fungible tokens, which are interchangeable and identical. These tokens can embody various digital assets like voting rights, virtual currencies, or staking tokens. They are also native `sdk.Coins` and come with a variety of native functionality
* **CW721**: This contract provides a standard approach for handling non-fungible tokens. These are unique and are not interchangeable with any other token. Examples could include ownership rights to a piece of artwork, or the licensing for a specific song.
* **CW20 \[Deprecated]:** Although not recommended, this contract provides another approach for handling fungible tokens. Similar to ERC-20 standard, contracts can implement this specification. CW20 contracts provide a standardized framework for the issuance, transfer, and tracking of fungible tokens.&#x20;



## Tokenfactory vs CW20

The Sei Foundation **strongly recommends** dapps use `TokenFactory` native `sdk.Coins` over CW20 tokens for the following reasons:

* **Performance:** Less computational cost and thus less gas fees for using natively integrated TokenFactory `sdk.Coins` as opposed to interacting with CW20 contracts
* **Security:** Tokens created with native module TokenFactory are `sdk.Coins` and share the same security model as the base blockchain. With CW20 smart contracts, there may be risks that poorly written or malicious contracts create security issues
* **Standardization:** Tokens created with native module TokenFactory are `sdk.Coins` and are directly integrated with the `bank` module. They offer better interoperability and are more accessible to bridging (like IBC)
* **Simplicity**: For basic fungible tokens that do not require extra functionality of a CW20 or similar smart contract, using native module like TokenFactory is simpler and more straightforward





\
