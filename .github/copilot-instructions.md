# Copilot Instructions for @kaytrust/did-near-resolver

## 🔭 Project Overview
This represents the **`@kaytrust/did-near-resolver`** repository, a **TypeScript SDK** designed to resolve Decentralized Identifiers (DIDs) on the NEAR Protocol (`did:near`). It complies with the W3C DID Core specification and integrates with the `did-resolver` library.

## 🏗 Architecture & Core Components
- **`NearDIDResolver` (`src/resolver.ts`)**: The core class implementing the resolution logic. It handles:
  - **DID Parsing**: Distinguishes between implicit DIDs (Base58 public keys) and named account DIDs (`user.near`, `user.testnet`).
  - **Network Routing**: Determines whether to query mainnet or testnet based on the DID suffix or configuration.
  - **Data Fetching**: Uses `near-api-js` to connect to NEAR RPC nodes and fetch account "FullAccess" keys to construct the DID Document.
- **`getResolver`**: Factory function compatible with the `did-resolver` library registry.

## 🛠 Developer Workflows
- **Build**: Run `npm run build` to compile TypeScript to JavaScript (`dist/`) using `tsc`.
- **Test**: Run `npm test` to execute Jest tests. 
  - **Note**: Check `__tests__/index.test.ts` for current testing patterns. The codebase has moved between mocking `near-api-js` and potentially running integration tests against live networks (mock code is currently commented out). Verify network connectivity if tests fail.

## 🧩 Key Patterns & Conventions
- **DID Formats**:
  - **Implicit**: `did:near:<base58-public-key>` (resolves directly from the key).
  - **Named**: `did:near:<account-id>` (e.g., `did:near:alice.testnet`) resolves by querying the account's access keys.
  - **Network Specific**: `did:near:<network>:<identifier>` (e.g., `did:near:testnet:alice`).
- **Configuration**: The resolver supports both single-network init and multi-network configuration objects passed to the constructor.
- **Account Validation**: Internally regex-checks identifiers (`src/resolver.ts`) before attempting resolution to fail fast on invalid DIDs.

## 📦 Dependencies & Integration
- **`near-api-js`**: Primary client for interactions with NEAR RPC nodes.
- **`did-resolver`**: Standard interface for DID resolution conformant with the ecosystem.
- **`bs58`**: Used for encoding/decoding public keys.

## ⚠️ Important Implementation Details
- **Public Key Handling**: The resolver filters for keys with `permission: "FullAccess"` when constructing the DID Document verification methods.
- **Error Handling**: Throws explicit errors when accounts or access keys are not found (Review `getPublicKeyFromRPC` in `src/resolver.ts`).
