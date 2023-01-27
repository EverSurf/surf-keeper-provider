<p align="center">
    <h3 align="center">Surf Keeper browser provider</h3>
    <p align="center">Provider interface for Surf Keeper extension of the Everscale blockchain</p>
    <p align="center">
        <a href="/LICENSE">
            <img alt="GitHub" src="https://img.shields.io/github/license/broxus/everscale-inpage-provider" />
        </a>
        <a href="https://www.npmjs.com/package/everscale-inpage-provider">
            <img alt="npm" src="https://img.shields.io/npm/v/everscale-inpage-provider">
        </a>
    </p>
</p>

### How to install

```shell
npm install --save surf-keeper-provider
```
### Contents

- [`src/index.ts`](./src/index.ts) - main RPC provider description
- [`src/api.ts`](./src/api.ts) - RPC interface description
- [`src/models.ts`](./src/models.ts) - general models, used in RPC interface

### Methods

# Surf Extension methods

- **connect**
    
    Requests new permissions for current origin.
    Shows an approval window to the user.
    
    ```jsx
    input: {};
    output: {
    	isConnected: boolean; // Flag shows connection status for the current origin
    	address?: string; // Address of extension wallet
    	publicKey?: string; // Hex encoded public key
    };
    ```
    
    Example:
    
    ```jsx
    const result = await rpc.connect();
    ```
    
- **connectStatus**
    
    Requests new permissions for current origin.
    Shows an approval window to the user.
    
    ```jsx
    input: {};
    output: {
    	isConnected: boolean; // Flag shows connection status for the current origin
    	address?: string; // Address of extension wallet
    	publicKey?: string; // Hex encoded public key
    };
    ```
    
    Example:
    
    ```jsx
    const result = await rpc.connectStatus();
    ```
- **disconnect**
    
    Removes all permissions for current origin.
    
    ```jsx
    input: {};
    output: {
    	isConnected: boolean; // 	Flag shows connection status for the current origin; should return `false` as disconnect method execution result
    };
    ```
    
    Example:
    
    ```jsx
    const result = await rpc.disconnect();
    ```
    
- **sendMessage**
    
    Sends an internal message from the user account.
    Shows an approval window to the user.
    
    ```jsx
    input: {
    	abi: string; // Contract abi
    	address: string; // Address string
    	net: TONNetNameKey; // Name of network to send message in
    	callSet: {
    		functionName: string; // Name of contract function to be sent to the contract
    		input: Record<string, any>; // Input for the contract function
    		header?: FunctionHeader;
    	};
    	bounce: boolean; // Whether to bounce message back on error
    	amount: string; // Amount of nano EVER to send
    	action?: string; // Name of action to be performed by message send
    };
    output: {};
    ```
    
    Example:
    
    ```jsx
    const response = await rpc.sendMessage({
        amount: '2000000000', // in nano-tokens, i.e. 2 tokens
        bounce: true,
        callSet: {
            functionName: 'addComment',
            input: {
                comment: 'Test comment',
            },
        },
        net: 'mainnet',
        action: 'Create comment',
        address: '0:8959ea111cc0c85d996df0d16e530d584d5366618cfed9ab6a1754828bb78479',
        abi: '{"ABI version":2,"version":"2.3","header":["pubkey","time","expire"]...'
    });
    ```
    
- **sendTransaction**
    
    Sends transaction with provided params.
    
    ```jsx
    input: {
    	amount: string; // Amount of nano EVER to send
    	bounce: boolean; // Whether to bounce message back on error
    	comment: string; // Comment for the transaction to send it in payload
    	net: TONNetNameKey; // Name of network to send transaction in
    	to: string; // Address to send transaction to
    };
    output: {};
    ```
    
    Example:
    
    ```jsx
    const response = await rpc.sendTransaction({
        amount: '10000000000', // in nano-tokens, i.e. 10 tokens
        bounce: true,
        comment: 'check it out!',
        net: 'devnet',
        to: '0:b76b532fbe72307bff243b401d6792d5d01332ea294a0310c0ffdf874026f2b9',
    });
    ```
    
- **signData**
    
    Signs arbitrary data.
    
    ```jsx
    input: {
    	data: string; // Base64 encoded arbitrary bytes
    };
    output: {
    	signature: string; // Base64 encoded signature bytes (data is guaranteed to be 64 bytes long)
    };
    ```
    
    Example:
    
    ```jsx
    const response = await rpc.‘signData’({
        data: 'te6ccgEBAQEAKAAASw4E0p6AD5fz9JsGWfbBhP0Bwq9+jk0X3za9rhuI7A1H3DxC0QBw',
    });
    ```
### Example

```typescript
import {
    Address,
    ConnectResponse,
    ProviderRpcClient,
    hasSurfKeeperProvider
} from 'surf-keeper-provider';

const rpc = new ProviderRpcClient();

async function myApp() {
  if (!(await rpc.hasProvider())) {
    throw new Error('Extension is not installed');
  }

  const connectionInfo = await rpc.connect();
  if (connectionInfo == undefined) {
    throw new Error('Insufficient permissions');
  }

  const selectedAddress = connectionInfo.address;
  const isConnected = connectionInfo.isConnected;
  const publicKey = connectionInfo.publicKey;

  const transaction = await rpc
    .sendTransaction({
        amount: '10000000000',
        bounce: true,
        comment: 'check it out!',
        net: 'devnet',
        to: '0:b76b532fbe72307bff243b401d6792d5d01332ea294a0310c0ffdf874026f2b9'
    });
  console.log(transaction);

  const message = await rpc
    .sendMessage({
        amount: '2000000000', // in nano-tokens, i.e. 2 tokens
        bounce: true,
        callSet: {
            functionName: 'functionName',
            input: {
                comment: 'Test comment',
            },
        },
        net: 'mainnet',
        action: 'Create comment',
        address: '0:8959ea111cc0c85d996df0d16e530d584d5366618cfed9ab6a1754828bb78479',
        abi: abi
    });
  console.log(message);
}

const abi = {
  'ABI version': 2,
  'header': ['time', 'expire'],
  'functions': [{
    ...
  }],
  'data': [],
  'events': [],
} as const; // NOTE: `as const` is very important here

myApp().catch(console.error);
```

