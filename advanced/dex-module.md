# DEX module

The `dex` module is responsible for order placement and related trading functionality. One can build exchange dapp based this module. You can also find more details [here](https://github.com/sei-protocol/sei-chain/tree/master/x/dex).

To interact with the dex module, one can use either a `query` or `tx` message. Below are examples of how to interact, along with a brief overview of some common enums and structs used in the Appendix. Note that these commands have the usual other flags such as `--from, --chain_id, -fees, --gas, --broadcast-mode, etc.`

### Queries:

#### `GetTwaps`&#x20;

Description: gets time weighted average prices for specific assets

Usage: `seid q get-twaps $contract_addr $lookback`

Arguments: 1) contract address (string), 2) lookback (uint64)

Returns: `Twap[]`

Example: `seid q get-twaps $contract_addr 100 --from=$key_name --chain_id=sei_chain --fees=1000000usei --gas=50000000 --broadcast-mode=block`

#### `GetOrders`

Description: get orders by a specific account on the order book

Usage: `seid q get-orders $contract_addr $account`

Arguments: 1) contract address (string), 2) account (string)

Returns: Orders\[]

Example: `seid q get-orders $contract_addr $acc_addr --from=$key_name --chain_id=sei_chain --fees=1000000usei --gas=50000000 --broadcast-mode=block`

#### `GetOrderById`&#x20;

Description: get individual order by order ID

Usage: `seid q get-orders-by-id $contract_addr $price_denom $asset_denom $id`

Arguments: 1) contract address (string), 2) price\_denom (string), 3) asset\_denom (string), 4) id (uint64)

Returns: Order

Example: `seid q get-orders-by-id $contract_addr 'USDC' 'ATOM' $id --from=$key_name --chain_id=sei_chain --fees=1000000usei --gas=50000000 --broadcast-mode=block`

### Messages:

#### `PlaceOrders`&#x20;

Description: places orders with the order book

Usage: `seid tx place-orders $contract_addr $orders --amount $coins (optional)`

Arguments: 1) contract address (string), 2) list of orders

Note: orders arguments are split by a ?, and the following holds: position direction is arg\[0], price is arg\[1], quantity is arg\[2], price denom is args\[3], asset denom is args\[4], order type is args\[5], and data is args\[6]

Returns: `uint64[] OrderIds`

Example: `seid tx dex place-orders $contract_addr 'LONG?1.01?5?USDC?ATOM?LIMIT?{"leverage":"1","position_effect":"Open"}' --amount=1000000000uusdc -y --from=$key_name --chain_id=sei_chain --fees=1000000usei --gas=50000000 --broadcast-mode=block`&#x20;

#### **`CancelOrders`**&#x20;

Description: Bulk cancel orders with the dex order book

Usage: `seid tx cancel-orders $contract_addr $cancellations`

Arguments: 1) contract address (string), 2) list of cancellations

Note: cancellation arguments are split by a ?, and order id is arg\[0], position direction is arg\[1], price is arg\[2], price denom is args\[3], asset denom is args\[4]

Returns: None (`type MsgCancelOrdersResponse struct {}`)

Example: `seid tx dex cancel-orders $contract_addr '1?LONG?1.01?USDC?ATOM' --from=$key_name --chain_id=sei_chain --fees=1000000usei --gas=50000000 --broadcast-mode=block`



### Appendix

#### Enums:

```go
enum PositionDirection {
    LONG = 0;
    SHORT = 1;
}

enum PositionEffect {
    OPEN = 0;
    CLOSE = 1;
}

enum OrderType {
    LIMIT = 0;
    MARKET = 1;
    LIQUIDATION = 2;
    FOKMARKET = 3; // fill-or-kill market order
}

enum OrderStatus {
    PLACED = 0;
    FAILED_TO_PLACE = 1;
    CANCELLED = 2;
    FULFILLED = 3;
}

enum CancellationInitiator {
    USER = 0;
    LIQUIDATED = 1;
}
```

#### Structs:&#x20;

Pair:

```go
type Pair struct {
	PriceDenom string                                  
	AssetDenom string                                  
	Ticksize   *github_com_cosmos_cosmos_sdk_types.Dec 
}
```

Twap:

```go
type Twap struct {
	Pair            *Pair                                  
	Twap            github_com_cosmos_cosmos_sdk_types.Dec 
	LookbackSeconds uint64                                 
}
```

Order:&#x20;

```go
type Order struct {
	Id                uint64                                 
	Status            OrderStatus                            
	Account           string                                 
	ContractAddr      string                                 
	Price             github_com_cosmos_cosmos_sdk_types.Dec 
	Quantity          github_com_cosmos_cosmos_sdk_types.Dec 
	PriceDenom        string                                 
	AssetDenom        string                                 
	OrderType         OrderType                              
	PositionDirection PositionDirection                      
	Data              string                                 
	StatusDescription string                                 
}
```

Cancellation:

```go
type Cancellation struct {
	Id                uint64                                 
	Initiator         CancellationInitiator                  
	Creator           string                                 
	ContractAddr      string                                 
	PriceDenom        string                                 
	AssetDenom        string                                 
	PositionDirection PositionDirection                      
	Price             github_com_cosmos_cosmos_sdk_types.Dec 
}
```

&#x20;

