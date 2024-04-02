---
description: Examples of how to interact with sei-chain through Go.
---

# Go References

## Signing Transactions

Here's an example from our loadtest client, it [builds the transaction](https://github.com/sei-protocol/sei-chain/blob/main/loadtest/loadtest\_client.go#L158), sets the gas and fee limits and then [signs it](https://github.com/sei-protocol/sei-chain/blob/main/loadtest/tx.go) in Go&#x20;

```go
func (c *LoadTestClient) BuildTxs() (workgroups []*sync.WaitGroup, sendersList [][]func()) {
	...
			accountIdentifier := fmt.Sprint(account)
			accountKeyPath := c.SignerClient.GetTestAccountKeyPath(uint64(account))
			key := c.SignerClient.GetKey(accountIdentifier, "test", accountKeyPath)

			msgs, failureExpected, signer, gas, fee := c.generateMessage(config, key, config.MsgsPerTx)
			txBuilder := TestConfig.TxConfig.NewTxBuilder()
			_ = txBuilder.SetMsgs(msgs...)
			seqDelta := uint64(i / 2)
			mode := typestx.BroadcastMode_BROADCAST_MODE_SYNC
			if j == len(activeAccounts)-1 {
				mode = typestx.BroadcastMode_BROADCAST_MODE_BLOCK
			}
			sender := SendTx(signer, &txBuilder, mode, seqDelta, failureExpected, *c, gas, fee)
	...
}

func SendTx(
	key cryptotypes.PrivKey,
	txBuilder *client.TxBuilder,
	mode typestx.BroadcastMode,
	seqDelta uint64,
	failureExpected bool,
	loadtestClient LoadTestClient,
	gas uint64,
	fee int64,
) func() {
	(*txBuilder).SetGasLimit(gas)
	(*txBuilder).SetFeeAmount([]sdk.Coin{
		sdk.NewCoin("usei", sdk.NewInt(fee)),
	})
	loadtestClient.SignerClient.SignTx(loadtestClient.ChainID, txBuilder, key, seqDelta)
	...
}

func (sc *SignerClient) SignTx(chainID string, txBuilder *client.TxBuilder, privKey cryptotypes.PrivKey, seqDelta uint64) {
	var sigsV2 []signing.SignatureV2
	signerInfo := sc.GetAccountNumberSequenceNumber(privKey)
	accountNum := signerInfo.AccountNumber
	seqNum := signerInfo.SequenceNumber

	seqNum += seqDelta
	sigV2 := signing.SignatureV2{
		PubKey: privKey.PubKey(),
		Data: &signing.SingleSignatureData{
			SignMode:  TestConfig.TxConfig.SignModeHandler().DefaultMode(),
			Signature: nil,
		},
		Sequence: seqNum,
	}
	sigsV2 = append(sigsV2, sigV2)
	_ = (*txBuilder).SetSignatures(sigsV2...)
	sigsV2 = []signing.SignatureV2{}
	signerData := xauthsigning.SignerData{
		ChainID:       chainID,
		AccountNumber: accountNum,
		Sequence:      seqNum,
	}
	sigV2, _ = clienttx.SignWithPrivKey(
		TestConfig.TxConfig.SignModeHandler().DefaultMode(),
		signerData,
		*txBuilder,
		privKey,
		TestConfig.TxConfig,
		seqNum,
	)
	sigsV2 = append(sigsV2, sigV2)
	_ = (*txBuilder).SetSignatures(sigsV2...)
}
```

## Verifying Transactions&#x20;

This is another example from the loadtest client where it takes a list of TX Hashes and verifies the responses concurrently.&#x20;

```go
func (c *LoadTestClient) ValidateTxs() {
	defer c.Close()
	numTxs := len(c.TxHashList)
	resultChan := make(chan *types.TxResponse, numTxs)
	var waitGroup sync.WaitGroup

	if numTxs == 0 {
		return
	}

	for _, txHash := range c.TxHashList {
		waitGroup.Add(1)
		go func(txHash string) {
			defer waitGroup.Done()
			resultChan <- c.GetTxResponse(txHash)
		}(txHash)
	}

	go func() {
		waitGroup.Wait()
		close(resultChan)
	}()

	fmt.Printf("Validating %d Transactions... \n", len(c.TxHashList))
	waitGroup.Wait()

	notCommittedTxs := 0
	responseCodeMap := map[int]int{}
	responseStringMap := map[string]int{}
	for result := range resultChan {
		// If the result is nil then that means the transaction was not committed
		if result == nil {
			notCommittedTxs++
			continue
		}
		code := result.Code
		codeString := "ok"
		if code != 0 {
			codespace := result.Codespace
			error := sdkerrors.ABCIError(codespace, code, fmt.Sprintf("Error code=%d ", code))
			codeString = error.Error()
		}
		responseStringMap[codeString]++
		responseCodeMap[int(code)]++
	}

	fmt.Printf("Transactions not committed: %d\n", notCommittedTxs)
	pp.Printf("Response Code Mapping: \n %s \n", responseStringMap)
	IncrTxNotCommitted(notCommittedTxs)
	for reason, count := range responseStringMap {
		IncrTxProcessCode(reason, count)
	}
}
```

## Listening to events

Here's an example from our [oracle price-feeder](https://github.com/sei-protocol/sei-chain/blob/main/oracle/price-feeder/oracle/client/height\_updater.go), it listens to new height event from a chain running on the same host as the Oracle price-feeder service.&#x20;

```go
package client

import (
	"context"
	"fmt"
	"time"

	"github.com/rs/zerolog"
	tmrpcclient "github.com/tendermint/tendermint/rpc/client"
	tmtypes "github.com/tendermint/tendermint/types"
)

var (
	started                  = false
	queryEventNewBlockHeader = tmtypes.QueryForEvent(tmtypes.EventNewBlockHeaderValue)
	queryInterval            = 20 * time.Millisecond
)

// HeightUpdater is used to provide the updates of the latest chain
// It starts a goroutine to subscribe to new block event and send the latest block height to the channel
type HeightUpdater struct {
	Logger        zerolog.Logger
	LastHeight    int64
	ChBlockHeight chan int64
}

// Start will start a new goroutine subscribed to EventNewBlockHeader.
func (heightUpdater HeightUpdater) Start(
	ctx context.Context,
	rpcClient tmrpcclient.Client,
	logger zerolog.Logger,
) error {
	if !started {
		if err := rpcClient.Start(ctx); err != nil {
			return err
		}
		go heightUpdater.subscribe(ctx, rpcClient, logger)
		started = true
	}
	return nil
}

// subscribe listens to new blocks being made
// and updates the chain height.
func (heightUpdater HeightUpdater) subscribe(
	ctx context.Context,
	eventsClient tmrpcclient.EventsClient,
	logger zerolog.Logger,
) {
	for {
		eventData, err := tmrpcclient.WaitForOneEvent(ctx, eventsClient, queryEventNewBlockHeader.String())
		if err != nil {
			logger.Debug().Err(err).Msg("Failed to query EventNewBlockHeader")
		}
		eventDataNewBlockHeader, ok := eventData.(tmtypes.EventDataNewBlockHeader)
		if !ok {
			logger.Err(err).Msg("Failed to parse event from eventDataNewBlockHeader")
			continue
		} else {
			eventHeight := eventDataNewBlockHeader.Header.Height
			if eventHeight > heightUpdater.LastHeight {
				logger.Info().Msg(fmt.Sprintf("Received new Chain Height: %d", eventHeight))
				heightUpdater.LastHeight = eventHeight
				if len(heightUpdater.ChBlockHeight) < 1 {
					heightUpdater.ChBlockHeight <- eventHeight
				} else {
					// skip this block height since price feeder is still sending previous transaction
					logger.Info().Msg(fmt.Sprintf("Skipped Block Height: %d due to in progress tx", eventHeight))
				}
			}

		}
		time.Sleep(queryInterval)
	}
}
```



