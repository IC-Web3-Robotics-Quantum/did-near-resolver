# @kaytrust/did-near-resolver

A comprehensive TypeScript SDK to resolve Decentralized Identifiers (DIDs) on the NEAR Protocol (`did:near`).

## 🌟 Features

- **Standard Compliance**: Fully compliant with W3C DID Core specifications and compatible with the [`did-resolver`](https://github.com/decentralized-identity/did-resolver) library.
- **Dual Resolution Modes**:
  - **Named Accounts**: Resolves DIDs like `did:near:alice.near` directly from on-chain account keys (FullAccess keys).
  - **Registry DIDs**: Resolves Base58 DIDs (e.g., `did:near:CF5...`) via a configurable smart contract registry.
- **Multi-Network Support**: Configure multiple NEAR networks (Mainnet, Testnet) in a single instance.
- **Lightweight**: Built on top of `near-api-js`.

## 📦 Installation

```bash
npm install @kaytrust/did-near-resolver near-api-js bs58 did-resolver
```

Or with Yarn:

```bash
yarn add @kaytrust/did-near-resolver near-api-js bs58 did-resolver
```

## 🚀 Usage

### 1. Using with `did-resolver` (Recommended)

This is the standard integration for apps using the universal `did-resolver` library to handle multiple DID methods.

```typescript
import { Resolver } from 'did-resolver';
import { getResolver } from '@kaytrust/did-near-resolver';

// 1. Configure the NEAR resolver
const nearResolver = getResolver({
  networks: [
    {
      networkId: 'testnet',
      rpcUrl: 'https://rpc.testnet.near.org',
      contractId: 'registry.contract.testnet' // Smart contract for Base58 DIDs (optional)
    },
    {
      networkId: 'mainnet', // You can use 'near' or 'mainnet', both are equivalent
      rpcUrl: 'https://rpc.mainnet.near.org',
      contractId: 'registry.contract.near' // (optional)
    }
  ]
});

// 2. Instantiate the universal resolver
const resolver = new Resolver({
  ...nearResolver,
  // ... other DID resolvers (ethr, key, web, etc.)
});

// 3. Resolve a DID
const result = await resolver.resolve('did:near:alice.testnet');
console.log(result.didDocument);
```

### 2. Standalone Usage

You can use the `NearDIDResolver` class directly if you don't need the generic `did-resolver` interface.

```typescript
import { NearDIDResolver } from '@kaytrust/did-near-resolver';

const resolver = new NearDIDResolver({
  networks: [{
    networkId: 'testnet',
    rpcUrl: 'https://rpc.testnet.near.org',
    contractId: 'registry.contract.testnet'
  }]
});

const doc = await resolver.resolveDID('did:near:alice.testnet');
console.log(doc);
```

### 3. Quick Start (Single Network Shorthand)

For simple use cases targeting a single network:

```typescript
import { getResolver } from '@kaytrust/did-near-resolver';

const nearResolver = getResolver(
  'registry.contract.testnet',  // Contract ID
  'https://rpc.testnet.near.org', // RPC URL
  'testnet'                       // Network ID
);
```

## 🧩 DID Formats & Resolution Logic

The resolver distinguishes how to look up DIDs based on their format:

### Named Account DIDs
**Format**: `did:near:<account-id>`
- **Example**: `did:near:alice.testnet`
- **Resolution**:
  1. Parses the suffix (`alice.testnet`) to identify the NEAR network.
  2. Queries the NEAR RPC account details.
  3. Filters for `FullAccess` keys to populate the `verificationMethod` section of the DID Document.

### Registry DIDs (Base58)
**Format**: `did:near:<base58-identifier>`
- **Example**: `did:near:CF5RiJYh4EVmEt8UADTjoP3XaZo1NPWxv6w5TmkLqjpR`
- **Resolution**:
  1. Identifies the string as a Base58 identifier.
  2. Calls the `identity_owner` method on the configured `contractId`.
  3. Returns the DID Document associated with the owner registered in the contract.

## 🛠 Configuration

The `NearDIDResolver` accepts a configuration object with a `networks` array:

```typescript
type NetworkConfig = {
  rpcUrl: string;      // NEAR RPC endpoint
  networkId: string;   // 'testnet', 'mainnet', 'betanet' or custom
  contractId?: string;  // Smart contract for Registry DIDs (optional, only for base58)
}
```

## 💻 Development

### Build
Compile TypeScript to JavaScript:
```bash
npm run build
```

### Test
Run the test suite (Jest):
```bash
npm test
```

## 📄 License

[ISC](LICENSE)
