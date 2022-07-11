---
title: Ethereum RPC API
sidebar_label: Ethereum RPC API
sidebar_position: 3
hide_table_of_contents: true
---

import DocsCard from '@components/global/DocsCard';
import DocsCards from '@components/global/DocsCards';
import TOCInline from '@theme/TOCInline';

<head>
  <meta
    name="description"
    content="This page describes the different RPC methods available for the user. Some of this methods trigger a popup for the user to accept like personal_sign or eth_sendTransaction."
  />
  <style>{`
    :root {
      --doc-item-container-width: 60rem;
    }
  `}</style>
</head>

<intro-end />

This page describes the different RPC methods available for the user. Some of this methods trigger a popup for the user to accept like [`personal_sign`](#personal_sign) or [`eth_sendTransaction`](#ethsendtransaction).

## RPC Methods and Events

<TOCInline toc={toc} minHeadingLevel={3} maxHeadingLevel={3} />

## Detailed Documentation

### `eth_accounts`

Retrieve available accounts granted by the user.

##### Parameters

None

##### Return Type

`Address[]` - User accounts available.

##### Example

```typescript title="TypeScript"
await provider.request({ method: "eth_accounts", params: [] });
// ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
```

### `personal_sign`

Calculates an ethereum signature of the given data from the selected account.

:::caution Warning
This method adds the standard `"\x19Ethereum Signed Message:\n" + len(message)` prefix when calculating the signature hash.

`ecdsa(keccak256("\x19Ethereum Signed Message:\n" + len(message) + message))`
:::

##### Parameters

| Name    | Type                    | Description           |
| ------- | ----------------------- | --------------------- |
| Data    | `HexString` \| `String` | Data to sign.         |
| Account | `Address`               | Account to sign from. |

##### Return Type

`HexString<Signature>` - The ethereum ecdsa signature.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "personal_sign",
  params: ["0xdeadbeaf", "0x9b2055d370f73ec7d8a03e965129118dc8f5bf83"],
});
// "0xa3f20717a250c2b0b729b7e5becbff67fdaef7e0699da4de7ca5895b02a170a12d887fd3b17bfdce3481f10bea41f45ba9f709d39ce8325427b57afcfc994cee1b"
```

### `eth_sendTransaction`

Sends a new transaction to the network. If the transaction has no recipient and contains data, it creates a new contract. If the transaction has a recipient and data it executes a write contract call.

##### Parameters

| Name | Type                  | Description             |
| ---- | --------------------- | ----------------------- |
| Data | `Object<Transaction>` | The transaction object. |

```typescript title="Transaction"
type Transaction = {
  from: Address // The account the transaction is sent from.
  to?: Address // The transaction recipient.
  gas?: Number ?? 90000 // Gas units provided for transaction executon. It will return unused gas.
  gasPrice?: Number ?? currentGasPrice() // WEI paid for each gas unit used.
  value?: Number // WEI sent with this transaction to the recipient.
  data?: HexData // Encoded contract code or encoded contract call data.
}
```

##### Return Type

`HexString<Hash>` - The keccak-256 digest of the sent transaction.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "eth_sendTransaction",
  params: [
    {
      from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
      to: "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
      gas: "0x76c0", // 30400
      gasPrice: "0x9184e72a000", // 10000000000000
      value: "0x9184e72a", // 2441406250
      data: "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
    },
  ],
});
// "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
```

### `net_version`

Returns the current network id.

##### Parameters

None

##### Return Type

`ChainId` - The current network id. The full list of chain IDs is available at [chainlist.org](https://chainlist.org/). Some of the common ones are noted bellow.

```typescript title="ChainId"
type ChainId =
  | "1" // Ethereum Mainnet
  | "2" // Morden Testnet (deprecated)
  | "3" // Ropsten Testnet
  | "4" // Rinkeby Testnet
  | "5"; // Goerli Testnet
```

##### Example

```typescript title="TypeScript"
await provider.request({ method: "net_version", params: [] });
// "3"
```

### `web3_sha3`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_blockNumber`

Returns the number of the latest block mined.

##### Parameters

None

##### Return Type

`HexString<Integer>` - Number of the latest block.

##### Example

```typescript title="TypeScript"
await provider.request({ method: "eth_blockNumber", params: [] });
// "0xe659e6"
```

### `eth_call`

Executes a new message call immediately without creating a transaction on the blockchain.

##### Parameters

| Name  | Type                                                  | Description                           |
| ----- | ----------------------------------------------------- | ------------------------------------- |
| Data  | `Object<Transaction>`                                 | The transaction object.               |
| Block | `Number` \| `"latest"` \| `"earliest"` \| `"pending"` | Block number to execute this call in. |

```typescript title="Transaction"
type Transaction = {
  from: Address // The account the transaction is sent from.
  to?: Address // The transaction recipient.
  gas?: Number ?? 90000 // Gas units provided for transaction executon. It will return unused gas.
  gasPrice?: Number ?? currentGasPrice() // WEI paid for each gas unit used.
  value?: Number // WEI sent with this transaction to the recipient.
  data?: HexData // Encoded contract call data.
}
```

##### Return Type

`HexString<Any>` - The returned data of the contract execution.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "eth_call",
  params: [
    {
      from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
      to: "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
      gas: "0x76c0", // 30400
      gasPrice: "0x9184e72a000", // 10000000000000
      value: "0x9184e72a", // 2441406250
      data: "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
    },
    "latest",
  ],
});
// "0x9184e8f"
```

### `eth_chainId`

Returns the current network id.

##### Parameters

None

##### Return Type

`ChainId` - The current network id. The full list of chain IDs is available at [chainlist.org](https://chainlist.org/). Some of the common ones are noted bellow.

```typescript title="ChainId"
type ChainId =
  | "1" // Ethereum Mainnet
  | "2" // Morden Testnet (deprecated)
  | "3" // Ropsten Testnet
  | "4" // Rinkeby Testnet
  | "5"; // Goerli Testnet
```

##### Example

```typescript title="TypeScript"
await provider.request({ method: "net_version", params: [] });
// "3"
```

### `eth_estimateGas`

Generates an estimate of how much gas will be necessary to process a transaction on the blockchain. Generally the estimate is significantly greater than the actual gas used. The transaction will not be added to the blockchain.

##### Parameters

| Name | Type                  | Description             |
| ---- | --------------------- | ----------------------- |
| Data | `Object<Transaction>` | The transaction object. |

```typescript title="Transaction"
type Transaction = {
  from: Address // The account the transaction is sent from.
  to?: Address // The transaction recipient.
  gas?: Number ?? 90000 // Gas units provided for transaction executon. It will return unused gas.
  gasPrice?: Number ?? currentGasPrice() // WEI paid for each gas unit used.
  value?: Number // WEI sent with this transaction to the recipient.
  data?: HexData // Encoded contract call data.
}
```

##### Return Type

`HexString<Integer>` - Estimate in gas units.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "eth_estimateGas",
  params: [
    {
      from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
      to: "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
      gas: "0x76c0",
      gasPrice: "0x9184e72a000",
      value: "0x9184e72a",
      data: "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
    },
  ],
});
// "0x5cec"
```

### `eth_gasPrice`

Returns the current gas price in wei.

##### Parameters

None

##### Return Type

`HexString<Integer>` - Current gas price in wei.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "eth_gasPrice",
  params: [],
});
// "0x12a05f200"
```

### `eth_getBalance`

Returns the balance of the account of given address.

##### Parameters

| Name    | Type                                                  | Description                           |
| ------- | ----------------------------------------------------- | ------------------------------------- |
| Account | `Address`                                             | Account to check for balance.         |
| Block   | `Number` \| `"latest"` \| `"earliest"` \| `"pending"` | Block number to execute this call in. |

##### Return Type

`HexString<Integer>` - The current balance of the selected block in wei.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "eth_getBalance",
  params: ["0xc94770007dda54cF92009BFF0dE90c06F603a09f", "latest"],
});
// "0x2fe84e3113d7b"
```

# `eth_getBlockByHash`

Returns information about a block by hash.

##### Parameters

| Name                   | Type              | Description                                                                             |
| ---------------------- | ----------------- | --------------------------------------------------------------------------------------- |
| BlockHash              | `HexString<Hash>` | Hash of the selected block.                                                             |
| ShowTransactionDetails | `Boolean`         | If true returns the full transaction objects, otherwise the hashes of the transactions. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getBlockByNumber`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getBlockTransactionCountByHash`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getBlockTransactionCountByNumber`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getCode`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getFilterChanges`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getFilterLogs`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getLogs`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getStorageAt`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getTransactionByBlockHashAndIndex`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getTransactionByBlockNumberAndIndex`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getTransactionByHash`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getTransactionCount`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getTransactionReceipt`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getUncleByBlockHashAndIndex`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getUncleByBlockNumberAndIndex`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getUncleCountByBlockHash`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_getUncleCountByBlockNumber`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_newBlockFilter`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_newFilter`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_protocolVersion`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```

### `eth_uninstallFilter`

Returns the keccak-256 _(not the standarized sha3-256)_ of the given data.

##### Parameters

| Name | Type        | Description                      |
| ---- | ----------- | -------------------------------- |
| Data | `HexString` | Data to calculate the hash from. |

##### Return Type

`HexString` - The keccak-256 digest of the given data.

##### Example

```typescript title="TypeScript"
await provider.request({
  method: "web3_sha3",
  params: ["0x68656c6c6f20776f726c64"],
});
// "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
```