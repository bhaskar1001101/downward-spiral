# 1inch fusion+ polkadot extension - hackathon action plan

## project overview

extend 1inch fusion+ to enable bidirectional atomic swaps between ethereum and polkadot, preserving hashlock and timelock functionality for non-evm implementation while maintaining onchain execution requirements.

## technical architecture

### core components
- ethereum contracts (existing 1inch infrastructure)
- polkadot ink! contracts (new implementation)
- bridge infrastructure (oracle + relayer)
- cross-chain sdk extensions
- web ui for cross-chain swaps

## phase 1: foundation setup (day 1)

### repository setup
- fork `https://github.com/1inch/cross-chain-resolver-example`
- analyze existing resolver architecture and escrow patterns
- study `https://github.com/1inch/cross-chain-swap` for htlc implementation
- setup development environment for both ethereum and polkadot

### environment configuration
```bash
# ethereum testnet setup
export eth_rpc="https://sepolia.infura.io/v3/your_key"
export eth_private_key="your_private_key"

# polkadot testnet setup  
export polkadot_rpc="wss://rococo-contracts-rpc.polkadot.io"
export substrate_node="ws://127.0.0.1:9944"
```

### development tools installation
- foundry for ethereum contracts
- cargo-contract for ink! development
- substrate-contracts-node for local testing
- 1inch cross-chain sdk

## phase 2: polkadot contract development (days 1-2)

### ink! contract architecture

#### polkadot escrow factory
```rust
// contracts/polkadot-escrow-factory/lib.rs
#[ink::contract]
mod polkadot_escrow_factory {
    // factory for deploying escrow clones
    // manages escrow code hash and deployments
    // tracks deployed escrows by secret hash
}
```

#### polkadot escrow src/dst
```rust
// contracts/polkadot-escrow/lib.rs  
#[ink::contract]
mod polkadot_escrow {
    // htlc implementation with hashlock/timelock
    // supports claim, cancel, timeout operations
    // asset transfer capabilities
    // safety deposit management
}
```

#### polkadot resolver
```rust
// contracts/polkadot-resolver/lib.rs
#[ink::contract] 
mod polkadot_resolver {
    // resolver logic for polkadot side
    // interfaces with escrow factory
    // handles cross-chain message processing
    // manages partial fills and merkle trees
}
```

### core functionalities to implement
- `create_escrow_src` - lock tokens with hashlock/timelock
- `create_escrow_dst` - prepare tokens for user claim
- `claim_escrow` - reveal secret and claim tokens
- `cancel_escrow` - timeout handling and refunds
- `public_withdraw` - recovery mechanisms
- `partial_fill` - support for order splitting

### asset handling
- native dot token support
- integration with pallet-assets for other tokens
- cross-chain asset identification and mapping

## phase 3: bridge infrastructure (days 2-3)

### ethereum event watcher
```typescript
// bridge/ethereum-watcher.ts
class EthereumWatcher {
  // monitor escrow creation events
  // watch for secret revelation
  // track timeout and cancellation events
  // relay state changes to polkadot
}
```

### polkadot event watcher  
```typescript
// bridge/polkadot-watcher.ts
class PolkadotWatcher {
  // monitor polkadot escrow events
  // track contract deployments
  // watch for claim/cancel operations
  // relay updates to ethereum resolver
}
```

### message relayer service
```typescript
// bridge/relayer.ts
class CrossChainRelayer {
  // bidirectional message passing
  // state verification between chains
  // secret sharing coordination
  // timeout management
}
```

### oracle service
```typescript
// bridge/oracle.ts
class BridgeOracle {
  // verify ethereum transaction finality
  // confirm polkadot block confirmations
  // validate cross-chain state consistency
  // provide cryptographic proofs
}
```

## phase 4: sdk integration (day 3)

### extend 1inch cross-chain sdk
```typescript
// sdk/polkadot-integration.ts
export class PolkadotProvider {
  // implement polkadot blockchain provider
  // handle ink! contract interactions
  // manage polkadot wallet connections
  // support substrate address formats
}
```

### quote and order creation
```typescript
// sdk/cross-chain-quotes.ts
export class PolkadotQuoteProvider {
  // calculate eth <-> dot swap rates
  // estimate cross-chain gas costs
  // provide timelock parameters
  // generate order parameters
}
```

### secret management
```typescript
// sdk/secret-manager.ts  
export class SecretManager {
  // generate merkle trees for partial fills
  // coordinate secret sharing
  // handle cross-chain secret revelation
  // manage timeout scenarios
}
```

## phase 5: frontend development (days 3-4)

### ui components

#### swap interface
```tsx
// components/PolkadotSwapInterface.tsx
export function PolkadotSwapInterface() {
  // token selection (eth tokens <-> dot/polkadot assets)
  // amount input with balance checking
  // network selection (ethereum <-> polkadot)
  // slippage and timeout configuration
  // transaction preview and confirmation
}
```

#### transaction monitoring
```tsx
// components/CrossChainProgress.tsx
export function CrossChainProgress() {
  // step-by-step progress tracking
  // ethereum escrow deployment status
  // bridge relay confirmation
  // polkadot escrow creation
  // secret sharing and claim completion
}
```

#### wallet integration
```tsx  
// components/WalletConnector.tsx
export function WalletConnector() {
  // metamask for ethereum
  // polkadot.js extension for substrate
  // wallet switching between networks
  // account balance display
}
```

### state management
```typescript
// store/cross-chain-store.ts
export interface CrossChainState {
  // active swap orders
  // transaction history
  // network connection status
  // wallet balances across chains
}
```

## phase 6: testing and integration (day 4)

### contract testing
```rust
// tests/integration_tests.rs
#[ink::test]
fn test_cross_chain_swap_flow() {
  // full atomic swap simulation
  // timeout and recovery scenarios
  // partial fill functionality
  // error handling and edge cases
}
```

### bridge testing
```typescript
// tests/bridge.test.ts
describe('cross-chain bridge', () => {
  // ethereum to polkadot swap
  // polkadot to ethereum swap  
  // timeout handling
  // secret sharing coordination
});
```

### end-to-end testing
```typescript
// tests/e2e.test.ts
describe('full swap flow', () => {
  // user creates swap order
  // resolver executes both sides
  // secret coordination completes swap
  // funds transferred successfully
});
```

## phase 7: deployment and demo (day 5)

### testnet deployment

#### ethereum contracts
```bash
# deploy to sepolia testnet
forge script script/DeploySepolia.s.sol --rpc-url sepolia --broadcast
```

#### polkadot contracts
```bash
# deploy to rococo contracts parachain
cargo contract instantiate --url wss://rococo-contracts-rpc.polkadot.io
```

#### bridge services
```bash
# deploy bridge infrastructure  
npm run deploy:bridge-testnet
npm run start:relayer
npm run start:oracle
```

### demo preparation
- prepare test tokens on both networks
- setup resolver accounts with sufficient balances
- create demo swap scenarios
- document transaction flows
- record demo video

### documentation
- deployment addresses and configurations
- api documentation for contracts
- bridge setup instructions
- user guide for frontend
- technical architecture documentation

## deliverables

### core functionality
- [x] bidirectional atomic swaps eth <-> dot
- [x] hashlock and timelock preservation
- [x] onchain execution on both networks
- [x] partial fill support
- [x] timeout and recovery mechanisms

### stretch goals
- [ ] ui implementation
- [ ] multiple resolver support
- [ ] cross-parachain swaps via xcm
- [ ] advanced fee optimization
- [ ] mobile wallet integration

### technical artifacts
- polkadot ink! contracts (escrow factory, escrow, resolver)
- bridge infrastructure (watcher, relayer, oracle)
- sdk extensions for polkadot integration
- web frontend for cross-chain swaps
- comprehensive test suite
- deployment scripts and documentation

## resource allocation

### day 1: foundation and contracts (60% dev time)
- repository setup and analysis
- ink! contract skeleton implementation
- basic htlc functionality

### day 2: contract completion and bridge start (70% dev time)
- complete escrow contract functionality
- begin bridge watcher implementation
- integration testing setup

### day 3: bridge completion and sdk (80% dev time)
- finish relayer and oracle services
- sdk integration for polkadot
- begin frontend development

### day 4: frontend and testing (90% dev time)
- complete ui implementation
- end-to-end testing
- bug fixes and optimization

### day 5: deployment and demo (100% dev time)
- testnet deployment
- demo preparation
- final testing and documentation

## risk mitigation

### technical risks
- bridge oracle reliability - implement multiple verification methods
- secret sharing coordination - add redundant communication channels
- timeout edge cases - comprehensive testing of all scenarios
- substrate/ink! learning curve - allocate extra time for polkadot development

### timeline risks
- complex cross-chain testing - prioritize core functionality over advanced features
- frontend development scope - create minimal viable ui first
- deployment complexity - prepare deployment scripts early

## success metrics

### minimum viable product
- successful atomic swap execution eth -> dot
- successful atomic swap execution dot -> eth
- proper timeout and cancellation handling
- basic ui for swap creation

### hackathon winning criteria  
- seamless user experience across chains
- novel approach to cross-chain atomic swaps
- technical innovation in polkadot integration
- comprehensive demo and documentation
- potential for real-world adoption