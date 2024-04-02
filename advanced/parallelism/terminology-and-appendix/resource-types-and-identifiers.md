# Resource Types and Identifiers

## Resource Types

This is an appendix containing the resource types that Sei currently supports, and the store key that they represent. The source proto file can be found [here](https://github.com/sei-protocol/sei-cosmos/blob/dc36a8954a985baf50411769e517b1598c74d961/proto/cosmos/accesscontrol/constants.proto#L25).&#x20;

Below is the contents of the proto representing the resource type enum, usable for debugging / identifying dependencies. The comments also specify the hierarchy of the resource types, specifying which resource types are children of other resource types.&#x20;

```protobuf
enum ResourceType {
    ANY = 0;
    KV = 1; // child of ANY
    Mem = 2; // child of ANY
    DexMem = 3; // child of MEM
    KV_BANK = 4; // child of KV
    KV_STAKING = 5; // child of KV
    KV_WASM = 6; // child of KV
    KV_ORACLE = 7; // child of KV
    KV_DEX = 8; // child of KV
    KV_EPOCH = 9; // child of KV
    KV_TOKENFACTORY = 10; // child of KV
    KV_ORACLE_VOTE_TARGETS = 11; // child of KV_ORACLE
    KV_ORACLE_AGGREGATE_VOTES = 12; // child of KV_ORACLE
    KV_ORACLE_FEEDERS = 13; // child of KV_ORACLE
    KV_STAKING_DELEGATION = 14; // child of KV_STAKING
    KV_STAKING_VALIDATOR = 15; // child of KV_STAKING
    KV_AUTH = 16; // child of KV
    KV_AUTH_ADDRESS_STORE = 17; // child of KV
    KV_BANK_SUPPLY = 18; // child of KV_BANK
    KV_BANK_DENOM = 19; // child of KV_BANK
    KV_BANK_BALANCES = 20; // child of KV_BANK
    KV_TOKENFACTORY_DENOM = 21; // child of KV_TOKENFACTORY
    KV_TOKENFACTORY_METADATA = 22; // child of KV_TOKENFACTORY
    KV_TOKENFACTORY_ADMIN = 23; // child of KV_TOKENFACTORY
    KV_TOKENFACTORY_CREATOR = 24; // child of KV_TOKENFACTORY
    KV_ORACLE_EXCHANGE_RATE = 25; // child of KV_ORACLE
    KV_ORACLE_VOTE_PENALTY_COUNTER = 26; // child of KV_ORACLE
    KV_ORACLE_PRICE_SNAPSHOT = 27; // child of KV_ORACLE

    KV_STAKING_VALIDATION_POWER = 28; // child of KV_STAKING
    KV_STAKING_TOTAL_POWER = 29; // child of KV_STAKING
    KV_STAKING_VALIDATORS_CON_ADDR = 30; // child of KV_STAKING
    KV_STAKING_UNBONDING_DELEGATION = 31; // child of KV_STAKING
    KV_STAKING_UNBONDING_DELEGATION_VAL = 32; // child of KV_STAKING
    KV_STAKING_REDELEGATION = 33; // child of KV_STAKING
    KV_STAKING_REDELEGATION_VAL_SRC = 34; // child of KV_STAKING
    KV_STAKING_REDELEGATION_VAL_DST = 35; // child of KV_STAKING
    KV_STAKING_REDELEGATION_QUEUE = 36; // child of KV_STAKING
    KV_STAKING_VALIDATOR_QUEUE = 37; // child of KV_STAKING
    KV_STAKING_HISTORICAL_INFO = 38; // child of KV_STAKING
    KV_STAKING_UNBONDING = 39; // child of KV_STAKING
    KV_STAKING_VALIDATORS_BY_POWER = 41; // child of KV_STAKING

    KV_DISTRIBUTION = 40; // child of KV
    KV_DISTRIBUTION_FEE_POOL = 42; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_PROPOSER_KEY = 43; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_OUTSTANDING_REWARDS = 44; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_DELEGATOR_WITHDRAW_ADDR = 45; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_DELEGATOR_STARTING_INFO = 46; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_VAL_HISTORICAL_REWARDS = 47; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_VAL_CURRENT_REWARDS = 48; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_VAL_ACCUM_COMMISSION = 49; // child of KV_DISTRIBUTION
    KV_DISTRIBUTION_SLASH_EVENT = 50; // child of KV_DISTRIBUTION

    KV_DEX_CONTRACT_LONGBOOK = 51; // child of KV_DEX
    KV_DEX_CONTRACT_SHORTBOOK = 52; // child of KV_DEX
    KV_DEX_SETTLEMENT = 53; // child of KV_DEX
    KV_DEX_PAIR_PREFIX = 54; // child of KV_DEX
    KV_DEX_TWAP = 55; // child of KV_DEX
    KV_DEX_PRICE = 56; // child of KV_DEX
    KV_DEX_SETTLEMENT_ENTRY = 57; // child of KV_DEX
    KV_DEX_REGISTERED_PAIR = 58; // child of KV_DEX
    KV_DEX_PRICE_TICK_SIZE = 59; // child of KV_DEX
    KV_DEX_ORDER = 60; // child of KV_DEX
    KV_DEX_CANCEL = 61; // child of KV_DEX
    KV_DEX_ACCOUNT_ACTIVE_ORDERS = 62; // child of KV_DEX
    KV_DEX_REGISTERED_PAIR_COUNT = 63; // child of KV_DEX
    KV_DEX_ASSET_LIST = 64; // child of KV_DEX
    KV_DEX_NEXT_ORDER_ID = 65; // child of KV_DEX
    KV_DEX_NEXT_SETTLEMENT_ID = 66; // child of KV_DEX
    KV_DEX_MATCH_RESULT = 67; // child of KV_DEX
    KV_DEX_SETTLEMENT_ORDER_ID = 68; // child of KV_DEX
    KV_DEX_ORDER_BOOK = 69; // child of KV_DEX
    KV_DEX_QUANTITY_TICK_SIZE = 70; // child of KV_DEX

    KV_ACCESSCONTROL = 71; // child of KV
    KV_ACCESSCONTROL_WASM_DEPENDENCY_MAPPING = 72; // child of KV_ACCESSCONTROL

    KV_WASM_CODE = 73; // child of KV_WASM
    KV_WASM_CONTRACT_ADDRESS = 74; // child of KV_WASM
    KV_WASM_CONTRACT_STORE = 75; // child of KV_WASM
    KV_WASM_SEQUENCE_KEY = 76; // child of KV_WASM
    KV_WASM_CONTRACT_CODE_HISTORY = 77; // child of KV_WASM
    KV_WASM_CONTRACT_BY_CODE_ID = 78; // child of KV_WASM
    KV_WASM_PINNED_CODE_INDEX = 79; // child of KV_WASM

    KV_AUTH_GLOBAL_ACCOUNT_NUMBER = 80; // child of KV_AUTH
}
```

## ResourceTypePrefix

This is an appendix containing the mapping between `StoreKey` and `ResourceType` used in sei-cosmos. The hex definition of the prefix can be found in the corresponding module's `/types/key.go` , for example `wasm` module ResourceTypePrefix definition [here](https://github.com/CosmWasm/wasmd/blob/main/x/wasm/types/keys.go#L26-L39).&#x20;

The source file of the `StoreKeyToResourceTypePrefixMap` can be found [here](https://github.com/sei-protocol/sei-chain/blob/master/aclmapping/utils/resource\_type.go#L21).

```go
var StoreKeyToResourceTypePrefixMap = aclsdktypes.StoreKeyToResourceTypePrefixMap{
	aclsdktypes.ParentNodeKey: {
		aclsdktypes.ResourceType_ANY: aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV:  aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_Mem: aclsdktypes.EmptyPrefix,
	},
	dextypes.StoreKey: {
		aclsdktypes.ResourceType_KV_DEX:                    aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_DexMem:                    aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_DEX_CONTRACT_LONGBOOK:  dextypes.KeyPrefix(dextypes.LongBookKey),
		aclsdktypes.ResourceType_KV_DEX_CONTRACT_SHORTBOOK: dextypes.KeyPrefix(dextypes.ShortBookKey),
		// pricedenom and assetdenoms are the prefixes
		aclsdktypes.ResourceType_KV_DEX_PAIR_PREFIX:           aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_DEX_TWAP:                  dextypes.KeyPrefix(dextypes.TwapKey),
		aclsdktypes.ResourceType_KV_DEX_PRICE:                 dextypes.KeyPrefix(dextypes.PriceKey),
		aclsdktypes.ResourceType_KV_DEX_SETTLEMENT_ENTRY:      dextypes.KeyPrefix(dextypes.SettlementEntryKey),
		aclsdktypes.ResourceType_KV_DEX_REGISTERED_PAIR:       dextypes.KeyPrefix(dextypes.RegisteredPairKey),
		aclsdktypes.ResourceType_KV_DEX_ORDER:                 dextypes.KeyPrefix(dextypes.OrderKey),
		aclsdktypes.ResourceType_KV_DEX_CANCEL:                dextypes.KeyPrefix(dextypes.CancelKey),
		aclsdktypes.ResourceType_KV_DEX_ACCOUNT_ACTIVE_ORDERS: dextypes.KeyPrefix(dextypes.AccountActiveOrdersKey),
		aclsdktypes.ResourceType_KV_DEX_ASSET_LIST:            dextypes.KeyPrefix(dextypes.AssetListKey),
		aclsdktypes.ResourceType_KV_DEX_NEXT_ORDER_ID:         dextypes.KeyPrefix(dextypes.NextOrderIDKey),
		aclsdktypes.ResourceType_KV_DEX_NEXT_SETTLEMENT_ID:    dextypes.KeyPrefix(dextypes.NextSettlementIDKey),
		aclsdktypes.ResourceType_KV_DEX_MATCH_RESULT:          dextypes.KeyPrefix(dextypes.MatchResultKey),
		aclsdktypes.ResourceType_KV_DEX_ORDER_BOOK:            dextypes.KeyPrefix(dextypes.NextOrderIDKey),
		// SETTLEMENT keys are prefixed with account and order id
		aclsdktypes.ResourceType_KV_DEX_SETTLEMENT_ORDER_ID: aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_DEX_SETTLEMENT:          aclsdktypes.EmptyPrefix,
	},
	banktypes.StoreKey: {
		aclsdktypes.ResourceType_KV_BANK:          aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_BANK_BALANCES: banktypes.BalancesPrefix,
		aclsdktypes.ResourceType_KV_BANK_SUPPLY:   banktypes.SupplyKey,
		aclsdktypes.ResourceType_KV_BANK_DENOM:    banktypes.DenomMetadataPrefix,
	},
	authtypes.StoreKey: {
		aclsdktypes.ResourceType_KV_AUTH:                       aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_AUTH_ADDRESS_STORE:         authtypes.AddressStoreKeyPrefix,
		aclsdktypes.ResourceType_KV_AUTH_GLOBAL_ACCOUNT_NUMBER: authtypes.GlobalAccountNumberKey,
	},
	distributiontypes.StoreKey: {
		aclsdktypes.ResourceType_KV_DISTRIBUTION:                         aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_FEE_POOL:                distributiontypes.FeePoolKey,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_PROPOSER_KEY:            distributiontypes.ProposerKey,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_OUTSTANDING_REWARDS:     distributiontypes.ValidatorOutstandingRewardsPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_DELEGATOR_WITHDRAW_ADDR: distributiontypes.DelegatorWithdrawAddrPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_DELEGATOR_STARTING_INFO: distributiontypes.DelegatorStartingInfoPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_VAL_HISTORICAL_REWARDS:  distributiontypes.ValidatorHistoricalRewardsPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_VAL_CURRENT_REWARDS:     distributiontypes.ValidatorCurrentRewardsPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_VAL_ACCUM_COMMISSION:    distributiontypes.ValidatorAccumulatedCommissionPrefix,
		aclsdktypes.ResourceType_KV_DISTRIBUTION_SLASH_EVENT:             distributiontypes.ValidatorSlashEventPrefix,
	},
	oracletypes.StoreKey: {
		aclsdktypes.ResourceType_KV_ORACLE:                      aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_ORACLE_VOTE_TARGETS:         oracletypes.VoteTargetKey,
		aclsdktypes.ResourceType_KV_ORACLE_AGGREGATE_VOTES:      oracletypes.AggregateExchangeRateVoteKey,
		aclsdktypes.ResourceType_KV_ORACLE_FEEDERS:              oracletypes.FeederDelegationKey,
		aclsdktypes.ResourceType_KV_ORACLE_PRICE_SNAPSHOT:       oracletypes.PriceSnapshotKey,
		aclsdktypes.ResourceType_KV_ORACLE_EXCHANGE_RATE:        oracletypes.ExchangeRateKey,
		aclsdktypes.ResourceType_KV_ORACLE_VOTE_PENALTY_COUNTER: oracletypes.VotePenaltyCounterKey,
	},
	stakingtypes.StoreKey: {
		aclsdktypes.ResourceType_KV_STAKING:                          aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_STAKING_VALIDATION_POWER:         stakingtypes.LastValidatorPowerKey,
		aclsdktypes.ResourceType_KV_STAKING_TOTAL_POWER:              stakingtypes.LastTotalPowerKey,
		aclsdktypes.ResourceType_KV_STAKING_VALIDATOR:                stakingtypes.ValidatorsKey,
		aclsdktypes.ResourceType_KV_STAKING_VALIDATORS_CON_ADDR:      stakingtypes.ValidatorsByConsAddrKey,
		aclsdktypes.ResourceType_KV_STAKING_VALIDATORS_BY_POWER:      stakingtypes.ValidatorsByPowerIndexKey,
		aclsdktypes.ResourceType_KV_STAKING_DELEGATION:               stakingtypes.DelegationKey,
		aclsdktypes.ResourceType_KV_STAKING_UNBONDING_DELEGATION:     stakingtypes.UnbondingDelegationKey,
		aclsdktypes.ResourceType_KV_STAKING_UNBONDING_DELEGATION_VAL: stakingtypes.UnbondingDelegationByValIndexKey,
		aclsdktypes.ResourceType_KV_STAKING_REDELEGATION:             stakingtypes.RedelegationKey,
		aclsdktypes.ResourceType_KV_STAKING_REDELEGATION_VAL_SRC:     stakingtypes.RedelegationByValSrcIndexKey,
		aclsdktypes.ResourceType_KV_STAKING_REDELEGATION_VAL_DST:     stakingtypes.RedelegationByValDstIndexKey,
		aclsdktypes.ResourceType_KV_STAKING_UNBONDING:                stakingtypes.UnbondingQueueKey,
		aclsdktypes.ResourceType_KV_STAKING_REDELEGATION_QUEUE:       stakingtypes.RedelegationQueueKey,
		aclsdktypes.ResourceType_KV_STAKING_VALIDATOR_QUEUE:          stakingtypes.ValidatorQueueKey,
		aclsdktypes.ResourceType_KV_STAKING_HISTORICAL_INFO:          stakingtypes.HistoricalInfoKey,
	},
	tokenfactorytypes.StoreKey: {
		aclsdktypes.ResourceType_KV_TOKENFACTORY:          aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_TOKENFACTORY_DENOM:    []byte(tokenfactorytypes.DenomsPrefixKey),
		aclsdktypes.ResourceType_KV_TOKENFACTORY_METADATA: []byte(tokenfactorytypes.DenomAuthorityMetadataKey),
		aclsdktypes.ResourceType_KV_TOKENFACTORY_ADMIN:    []byte(tokenfactorytypes.AdminPrefixKey),
		aclsdktypes.ResourceType_KV_TOKENFACTORY_CREATOR:  []byte(tokenfactorytypes.AdminPrefixKey),
	},
	epochtypes.StoreKey: {
		aclsdktypes.ResourceType_KV_EPOCH: aclsdktypes.EmptyPrefix,
	},
	acltypes.StoreKey: {
		aclsdktypes.ResourceType_KV_ACCESSCONTROL:                         aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_ACCESSCONTROL_WASM_DEPENDENCY_MAPPING: acltypes.GetWasmMappingKey(),
	},
	wasmtypes.StoreKey: {
		aclsdktypes.ResourceType_KV_WASM:                       aclsdktypes.EmptyPrefix,
		aclsdktypes.ResourceType_KV_WASM_CODE:                  wasmtypes.CodeKeyPrefix,
		aclsdktypes.ResourceType_KV_WASM_CONTRACT_ADDRESS:      wasmtypes.ContractKeyPrefix,
		aclsdktypes.ResourceType_KV_WASM_CONTRACT_STORE:        wasmtypes.ContractStorePrefix,
		aclsdktypes.ResourceType_KV_WASM_SEQUENCE_KEY:          wasmtypes.SequenceKeyPrefix,
		aclsdktypes.ResourceType_KV_WASM_CONTRACT_CODE_HISTORY: wasmtypes.ContractCodeHistoryElementPrefix,
		aclsdktypes.ResourceType_KV_WASM_CONTRACT_BY_CODE_ID:   wasmtypes.ContractByCodeIDAndCreatedSecondaryIndexPrefix,
		aclsdktypes.ResourceType_KV_WASM_PINNED_CODE_INDEX:     wasmtypes.PinnedCodeIndexPrefix,
	},
}
```
