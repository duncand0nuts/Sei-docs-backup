# Bridging

Bridging is a vital process that enables the movement of tokens across different blockchains. Sei currently supports bridging through [Wormhole](https://wormhole.com/) and [Axelar](https://axelar.network/).&#x20;

### Wormhole&#x20;

When bridging through Wormhole's Portal Bridge, your source assets are locked in a smart contract and a Wormhole-wrapped asset is minted on the target chain. These wrapped assets can then be swapped on an exchange for other assets on the target chain. Wormhole can be used to transfer tokens in from other blockchains to Sei or to transfer Sei tokens out to other blockchains.&#x20;

### Axelar

Axelar is a blockchain that connects blockchains, enabling secure interchain communication and token transfers. When bridging through Axelar, your source assets are deposited into a one-time address. Axelar Network will then confirm the deposit and transfer the funds to the destination address on the appropriate chain.

### Token address list

The following table lists the address associated with tokens bridged to Sei through Wormhole. This can be used as a reference to verify the legitimacy of tokens when you bridge.



{% hint style="info" %}
These tokens addresses are also available in our [registry](https://github.com/sei-protocol/registry) on GitHub.&#x20;
{% endhint %}

<table><thead><tr><th width="184.33333333333331">Token Name</th><th width="137">Symbol</th><th width="164">Source Chain</th><th>Denom</th></tr></thead><tbody><tr><td>USD Coin (Axelar)</td><td>axlUSDC</td><td>Ethereum, Polygon, Arbitrum, BNB Smart Chain</td><td>ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349</td></tr><tr><td>Osmosis</td><td>OSMO</td><td>Osmosis</td><td>ibc/ED07A3391A112B175915CD8FAF43A2DA8E4790EDE12566649D0C2F97716B8518</td></tr><tr><td>Atom</td><td>ATOM</td><td>Osmosis</td><td>ibc/6CDD4663F2F09CD62285E2D45891FC149A3568E316CE3EBBE201A71A78A69388</td></tr><tr><td>Wrapped Ether (Wormhole)</td><td>WETH</td><td>Ethereum</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/4tLQqCLaoKKfNFuPjA9o39YbKUwhR1F8N29Tz3hEbfP2</td></tr><tr><td>USD Coin (Wormhole)</td><td>USDCet</td><td>Ethereum</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/Hq4tuDzhRBnxw3tFA5n6M52NVMVcC19XggbyDiJKCD6H</td></tr><tr><td>Wrapped BTC (Wormhole)</td><td>WBTC</td><td>Ethereum</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/7omXa4gryZ5NiBmLep7JsTtTtANCVKXwT9vbN91aS1br</td></tr><tr><td>USD Coin (Wormhole)</td><td>USDCar</td><td>Arbitrum</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/7edDfnf4mku8So3t4Do215GNHwASEwCWrdhM5GqD51xZ</td></tr><tr><td>USD Coin (Wormhole)</td><td>USDCpo</td><td>Polygon</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/DUVFMY2neJdL8aE4d3stcpttDDm5aoyfGyVvm29iA9Yp</td></tr><tr><td>USD Coin (Wormhole)</td><td>USDCop</td><td>Optimism</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/3VKKYtbQ9iq8f9CaZfgR6Cr3TUj6ypXPAn6kco6wjcAu</td></tr><tr><td>USD Coin (Wormhole)</td><td>USDCso</td><td>Solana</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/9fELvUhFo6yWL34ZaLgPbCPzdk9MD1tAzMycgH45qShH</td></tr><tr><td>Binance-Peg BSC-USD </td><td>USDTbs</td><td>BNB Smart Chain</td><td>factory/sei189adguawugk3e55zn63z8r9ll29xrjwca636ra7v7gxuzn98sxyqwzt47l/871jbn9unTavWsAe83f2Ma9GJWSv6BKsyWYLiQ6z3Pva</td></tr></tbody></table>

