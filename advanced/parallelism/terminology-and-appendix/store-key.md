# Store Key

This is an appendix containing the StoreKey used by sei-cosmos modules. You can also find these definitions in the `key.go` file of each module, for example the StoreKey`acc` of the auth module [here](https://github.com/sei-protocol/sei-cosmos/blob/8520877281aab3bee542abe60580d2e36752790c/x/auth/types/keys.go#L12).&#x20;

You can refer to the [Identifiers](resource-types-and-identifiers.md#resourcetypeprefix) page to find the corresponding identifer prefix.

```protobuf
StoreKey => Module Name | ResourceType key

"ParentNode" => x/accesscontrol | aclsdktypes.ParentNodeKey
"acc" => x/auth | authtypes.StoreKey
"dex" => x/dex | dextypes.StoreKey
"bank" => x/bank | banktypes.StoreKey
"distribution" => x/distribution | distributiontypes.StoreKey
"oracle" => x/oracle | oracletypes.StoreKey
"staking" => x/staking | stakingtypes.StoreKey
"tokenfactory" => x/tokenfactory | tokenfactorytypes.StoreKey
"epoch" => x/epoch | epochtypes.StoreKey
"aclaccesscontrol" => x/accesscontrol | acltypes.StoreKey
"wasm" => x/wasm | wasmtypes.StoreKey
```
