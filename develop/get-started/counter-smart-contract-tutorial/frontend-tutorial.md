---
description: Create and deploy a simple frontend locally
---

# Frontend Tutorial



{% hint style="info" %}
Please complete [Counter tutorial](./) to deploy the counter smart contract before continuing with this page
{% endhint %}

In this tutorial, we'll go over how to:

* Set up your frontend project
* Connect to a Sei wallet
* Query and display the current count
* Execute the counter smart contract's `increment` function

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

* Node.js
* Yarn or NPM
* A supported chrome based wallet extension
  * [Compass](https://chrome.google.com/webstore/detail/compass-wallet-for-sei/anokgmphncpekkhclmingpimjmcooifb)
  * [Fin](https://chrome.google.com/webstore/detail/fin-wallet-for-sei/dbgnhckhnppddckangcjbkjnlddbjkna)

### Creating a React project

To create a project from scratch we recommend using the typescript template from Vite, which makes development and debugging much easier.

```bash
yarn create vite my-counter-frontend --template react-ts
```

This will create a new folder with a React project that uses typescript. Open this folder in your favorite IDE.

### Install dependencies

From a terminal at the root of this project install the required project dependencies including **@sei-js/core** and **@sei-js/react** as well as some node polyfill libraries.

```bash
yarn && yarn add @sei-js/core @sei-js/react
```

or

```bash
npm install && npm install @sei-js/core @sei-js/react
```

### Update boilerplate UI

Replace your `App.tsx` file with the following code to set up a SeiWalletProvider context, define your chain info, and set connection URLs.

{% tabs %}
{% tab title="Typescript (highly recommended)" %}
<pre class="language-tsx"><code class="lang-tsx">import { SeiWalletProvider } from '@sei-js/react';
import './App.css';
import Home from './Home.tsx';
<strong>
</strong>function App() {
    return (
    	// Set up SeiWalletProvider for easy wallet connection and to use hooks in @sei-js/react
        &#x3C;SeiWalletProvider
	    chainConfiguration={{
	        chainId: 'atlantic-2',
		restUrl: 'https://rest.atlantic-2.seinetwork.io',
		rpcUrl: 'https://rpc.atlantic-2.seinetwork.io'
	    }}
	    wallets={['compass', 'fin']}>
	        &#x3C;Home />
	&#x3C;/SeiWalletProvider>
    );
}

export default App;
</code></pre>
{% endtab %}

{% tab title="Javascript" %}
<pre class="language-jsx"><code class="lang-jsx">import { SeiWalletProvider } from '@sei-js/react';
import './App.css';
import Home from './Home.jsx';
<strong>
</strong>function App() {
    return (
    	// Set up SeiWalletProvider for easy wallet connection and to use hooks in @sei-js/react
        &#x3C;SeiWalletProvider
	    chainConfiguration={{
	        chainId: 'atlantic-2',
		restUrl: 'https://rest.atlantic-2.seinetwork.io',
		rpcUrl: 'https://rpc.atlantic-2.seinetwork.io'
	    }}
	    wallets={['compass', 'fin']}>
	        &#x3C;Home />
	&#x3C;/SeiWalletProvider>
    );
}

export default App;
</code></pre>
{% endtab %}
{% endtabs %}

<details>

<summary>Detailed outline of App.tsx</summary>



**Wallet Provider Setup**

* Imports `SeiWalletProvider` from the `@sei-js/react` package.
* Wraps the `Home` component with the `SeiWalletProvider` to provide the wallet context.

**Chain Configuration**

* Specifies the `chainConfiguration` prop in `SeiWalletProvider` to set up the blockchain network:
  * `chainId`: Identifies the Sei network (in this case, `atlantic-2`).
  * `restUrl`: REST URL for the Sei (You may need your own provider).
  * `rpcUrl`: RPC URL for the Sei (You may need your own provider).

**Supported Wallets**

* Sets up supported wallet types (`'compass', 'fin', 'leap', 'keplr'`) in the `wallets` prop.

</details>



### Add a \<Home/> component

Create a new file called Home.tsx inside your `src` directory with the following code.

{% tabs %}
{% tab title="Typescript (highly recommended)" %}
```tsx
import { useCallback, useEffect, useState } from 'react';
import { useCosmWasmClient, useSigningCosmWasmClient, useWallet, WalletConnectButton } from '@sei-js/react';

const CONTRACT_ADDRESS = 'YOUR_COUNTER_CONTRACT ADDRESS'; // (atlantic-2 example) sei18g4g35mhy5s88nshpa6flvpj9ex6u88l6mhjmzjchnrfa7xr00js0gswru

function Home() {
	const [count, setCount] = useState<number | undefined>();
	const [error, setError] = useState<string>('');
	const [isIncrementing, setIsIncrementing] = useState<boolean>(false);

	// Helpful hook for getting the currently connected wallet and chain info
	const { connectedWallet, accounts } = useWallet();

	// For querying cosmwasm smart contracts
	const { cosmWasmClient: queryClient } = useCosmWasmClient();
	
	// For executing messages on cosmwasm smart contracts
	const { signingCosmWasmClient: signingClient } = useSigningCosmWasmClient();

	const fetchCount = useCallback(async () => {
	    const response = await queryClient?.queryContractSmart(CONTRACT_ADDRESS, { get_count: {} });
	    return response?.count;
	}, [queryClient]);

	useEffect(() => {
	    fetchCount().then(setCount);
	}, [connectedWallet, fetchCount]);

	const incrementCounter = async () => {
	    setIsIncrementing(true);
		try {
		    const senderAddress = accounts[0].address;

		    // Build message content
		    const msg = { increment: {} };

		   // Define gas price and limit
		    const fee = {
			amount: [{ amount: '0.1', denom: 'usei' }],
			gas: '200000'
		    };

		    // Call smart contract execute msg
		    await signingClient?.execute(senderAddress, CONTRACT_ADDRESS, msg, fee);

		    // Updates the counter state again
		    const updatedCount = await fetchCount();
		    setCount(updatedCount);
		
		    setIsIncrementing(false);
		    setError('');
		} catch (error) {
		    if (error instanceof Error) {
			setError(error.message);
		    } else {
			setError('unknown error');
		    }
		    setIsIncrementing(false);
		}
	};

	// Helpful component for wallet connection
	if (!connectedWallet) return <WalletConnectButton />;

	return (
	    <div>
		<h1>Count is: {count ? count : '---'}</h1>
		<button disabled={isIncrementing} onClick={incrementCounter}>
		    {isIncrementing ? 'incrementing...' : 'increment'}
		</button>
		{error && <p style={{ color: 'red' }}>{error}</p>}
	    </div>
	);
}

export default Home;
```
{% endtab %}

{% tab title="Javascript" %}
```jsx
import { useCallback, useEffect, useState } from 'react';
import { useCosmWasmClient, useSigningCosmWasmClient, useWallet, WalletConnectButton } from '@sei-js/react';

const CONTRACT_ADDRESS = 'YOUR_COUNTER_CONTRACT ADDRESS'; // (atlantic-2 example) sei18g4g35mhy5s88nshpa6flvpj9ex6u88l6mhjmzjchnrfa7xr00js0gswru

function Home() {
	const [count, setCount] = useState();
	const [error, setError] = useState('');
	const [isIncrementing, setIsIncrementing] = useState(false);

	// Helpful hook for getting the currently connected wallet and chain info
	const { connectedWallet, accounts } = useWallet();

	// For querying cosmwasm smart contracts
	const { cosmWasmClient: queryClient } = useCosmWasmClient();
	
	// For executing messages on cosmwasm smart contracts
	const { signingCosmWasmClient: signingClient } = useSigningCosmWasmClient();

	const fetchCount = useCallback(async () => {
	    const response = await queryClient?.queryContractSmart(CONTRACT_ADDRESS, { get_count: {} });
	    return response?.count;
	}, [queryClient]);

	useEffect(() => {
	    fetchCount().then(setCount);
	}, [connectedWallet, fetchCount]);

	const incrementCounter = async () => {
	    setIsIncrementing(true);
		try {
		    const senderAddress = accounts[0].address;

		    // Build message content
		    const msg = { increment: {} };

		   // Define gas price and limit
		    const fee = {
			amount: [{ amount: '0.1', denom: 'usei' }],
			gas: '200000'
		    };

		    // Call smart contract execute msg
		    await signingClient?.execute(senderAddress, CONTRACT_ADDRESS, msg, fee);

		    // Updates the counter state again
		    const updatedCount = await fetchCount();
		    setCount(updatedCount);
		
		    setIsIncrementing(false);
		    setError('');
		} catch (error) {
		    if (error instanceof Error) {
			setError(error.message);
		    } else {
			setError('unknown error');
		    }
		    setIsIncrementing(false);
		}
	};

	// Helpful component for wallet connection
	if (!connectedWallet) return <WalletConnectButton />;

	return (
	    <div>
		<h1>Count is: {count ? count : '---'}</h1>
		<button disabled={isIncrementing} onClick={incrementCounter}>
		    {isIncrementing ? 'incrementing...' : 'increment'}
		</button>
		{error && <p style={{ color: 'red' }}>{error}</p>}
	    </div>
	);
}

export default Home;
```
{% endtab %}
{% endtabs %}

<details>

<summary>Detailed outline of Home.tsx</summary>

**Render Logic**

* If no wallet is connected, it shows a `WalletConnectButton` which is a pre built component inside @sei-js/react.
* Otherwise, displays the counter value and an "Increment" button.
* Disables the increment button when a transaction is pending
* Catches and displays error messages, if any.

**Wallet and Client Setup**

* Utilizes `useWallet()` from `@sei-js/react` to get the connected wallet and accounts.
* Uses `useCosmWasmClient` and `useSigningCosmWasmClient` hooks from `@sei-js/react` to instantiate `queryClient` and `signingClient`. The signing client can be used for querying, but it is not recommended to use a signing client when only a query client is needed.

**Fetching Counter Value**

* `fetchCount` function uses `queryClient.queryContractSmart` to query the smart contract  (`CONTRACT_ADDRESS)` with the query `{ get_count: {} }`

**Incrementing Counter**

* `incrementCounter` function:
  * Extracts `senderAddress` from the connected wallet's accounts.
  * Builds a message (`msg`) with `{ increment: {} }` to be sent to the smart contract.
  * Specifies the transaction fee (`fee`) with gas price and gas limit.
  * Calls `signingClient.execute` to execute the contract with the built message and fee.

By leveraging @sei-js and its hooks, the component provides a way to connect to a wallet, query a CosmWasm smart contract to get the current counter state, and increment it with an execute message.

</details>

If you run this code as is, then it will throw an error - you need to tell it which contract to use. We have deployed a counter contract for you on the atlantic-2 testnet.&#x20;

To use it, set `CONTRACT_ADDRESS` to `sei18g4g35mhy5s88nshpa6flvpj9ex6u88l6mhjmzjchnrfa7xr00js0gswru`

### Run your application

Run `yarn dev` from the terminal and navigate to [http://localhost:5173/](http://localhost:5173/) to view your application.

{% hint style="info" %}
If this is your first time using Sei, you might see an error which says your account does not exist on chain.\
\
You can claim some testnet tokens for development purposes here: [https://atlantic-2.app.sei.io/faucet](https://atlantic-2.app.sei.io/faucet)\
\
Once you have funded your account, try the increment button again!
{% endhint %}

### Congrats!

You have now created a simple website for querying and executing a smart contract on Sei!

{% hint style="info" %}
Please see [Javascript reference](../../../advanced/javascript-reference.md) for more examples on how to do even more with your frontend.
{% endhint %}
