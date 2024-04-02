# Native Oracle

## Summary

Sei has an Oracle module to support asset exchange rate pricing for use by other modules and contracts. When validating for the network, participation as an Oracle is expected and required in order to ensure the most reliable and accurate pricing for assets.

For each voting window, a validator must provide asset pricing for the whitelisted assets. At the end of each of these voting windows, the votes are aggregated, and the chain computes a weighted median based on validator voting power to compute the effective exchange rate on a per asset basis.

### Price Feeder

Validator need to participate in providing pricing for the oracle to avoid being penalized and jailed. There is an oracle sidecar that can be configured to run as a systemd service to provide a more robust and configurable solution to providing oracle asset prices. The sidecar can be built with `make install-price-feeder` from the sei-chain repo dir, and needs an associated config file that specifies the account from which to send oracle votes along with additional configuration information such as assets to provide and providers to use for pricing information.

[**Example Config File**](https://github.com/sei-protocol/sei-chain/blob/master/oracle/price-feeder/config.example.toml)

Validators will need to update that with their own addresses and keyring information, and update the currency pairs to include assets that the oracle module what whitelisted.

Additionally, here is an example of a systemd service file for running the oracle sidecar:

```systemd
[Unit]
Description=Oracle Price Feeder
After=network.target

[Service]
User=root
Type=simple
Environment="PRICE_FEEDER_PASS={your keyring password for the oracle account here}"
# alternatively to `Environment`, validators can use `EnvironmentFile` to designate a file from which to get env var info
ExecStart=/root/go/bin/price-feeder /path/to/oracle/price_feeder_config.toml
Restart=on-failure
LimitNOFILE=6553500

[Install]
WantedBy=multi-user.target

```

### Oracle Penalties

There are penalties for non-participation and participation with bad data. Validators have a miss count and abstain count that is tracked. The abstain count tracks the number of voting windows that the validator did not submit a vote. The miss count tracks the number of voting windows that the validator's vote was improper (either had data that was outside of 1 standard deviation, or didn't contain asset prices for all whitelisted assets). There is a slash window, which is a period of blocks specified by oracle parameters, across which if the validator has too many misses and/or abstains, they are slashed and jailed. This is because oracle integrity and freshness is treated as a core chain service, and validators that do not participate in the upkeep of oracle asset pricing are penalized.

{% hint style="warning" %}
Misses and abstains both contribute to oracle penalization
{% endhint %}

### Feeder Delegation

If you want to run the oracle price feeder while signing from a different account (recommended), you can set that up by setting a feeder for your validator. This will allow the feeder account to perform oracle votes on the validator behalf as well.&#x20;

**Example**

```bash
seid tx oracle set-feeder $FEEDER_ADDR --from $WALLET_NAME --fees 2000usei --chain-id $CHAIN_ID
```

### Queries

#### Actives

Returns the current active denoms for which there are exchange rates

```bash
# Query
seid q oracle actives
# Example Response
actives:
- uatom
- ubtc
- ueth
- usei
```

#### Exchange Rates

Returns the current exchange rates for supported assets. Optionally can query for a specific denom by entering it as an additional param.

```bash
# Query
seid q oracle exchange-rates
# Example Response
- denom: uatom
  oracle_exchange_rate:
    exchange_rate: "10.813741474077133110"
    last_update: "7809045"
- denom: ubtc
  oracle_exchange_rate:
    exchange_rate: "28378.473894707711800815"
    last_update: "7806841"
- denom: ueth
  oracle_exchange_rate:
    exchange_rate: "1865.245279694096497292"
    last_update: "7809045"
```

#### TWAPs

Returns the time weighted average price for a given time interval in seconds. The maximum lookback is determined by oracle parameters.

```bash
# Query
seid q oracle twaps $LOOKBACK_SECONDS
# Example Response
oracle_twaps:
- denom: uatom
  lookback_seconds: "12"
  twap: "10.820090685543308091"
- denom: ubtc
  lookback_seconds: "12"
  twap: "28356.159567933482758461"
- denom: ueth
  lookback_seconds: "12"
  twap: "1865.748268727863484183"
```

#### Vote Penalty Counter

Returns the penalty counter for a validator queried by validator address.&#x20;

```bash
# Query
seid q oracle vote-penalty-counter $VALIDATOR_ADDR
# Example Response
vote_penalty_counter:
  abstain_count: "40618"
  miss_count: "0"
  success_count: "0"
```

#### Params

Returns the current parameters for the oracle module

```bash
# Query
seid q oracle params
# Example Response
params:
  lookback_duration: "3600"
  min_valid_per_window: "0.050000000000000000"
  reward_band: "0.020000000000000000"
  slash_fraction: "0.000100000000000000"
  slash_window: "201600"
  vote_period: "1"
  vote_threshold: "0.667000000000000000"
  whitelist:
  - name: uatom
  - name: usei
  - name: ueth
  - name: ubtc
```
