# SmartCache Integration Guide

## Folder Structure

```
contracts/
<<<<<<< HEAD
├── mycontracts/    # Original contract (without is_cacheable / opt_in_to_cache)
=======
├── cached-contract/          # Contract WITH is_cacheable helper (and stylus-cache-sdk)
>>>>>>> f97544e6af3451b21b8d1e8ba385e505da2403ba
├── cached-contract/          # Contract WITH is_cacheable helper (and stylus-cache-sdk)
│   ├── .cargo/
│   ├── src/
│   │   ├── lib.rs            # Contract + is_cacheable() injected
│   │   └── main.rs
<<<<<<< HEAD
│   └── ...
└── cached-contracts/     # Same contract WITH is_cacheable + opt_in_to_cache
=======
│   ├── .env.example
│   ├── .gitignore
│   ├── Cargo.toml            # Includes stylus-cache-sdk
│   └── rust-toolchain.toml
└── mycontract/               # Original contract (no caching helper)
>>>>>>> f97544e6af3451b21b8d1e8ba385e505da2403ba
│   ├── .env.example
│   ├── .gitignore
│   ├── Cargo.toml            # Includes stylus-cache-sdk
│   └── rust-toolchain.toml
└── mycontract/               # Original contract (no caching helper)
    ├── .cargo/
    ├── src/
    │   ├── lib.rs            # Your selected contract (counter, erc20, vending-machine, etc.)
    │   └── main.rs
    ├── .env.example
    ├── .gitignore
    ├── Cargo.toml
    └── rust-toolchain.toml
```

## Quick 3-step setup

1. **Import the cache SDK**

   Add the crate to your `Cargo.toml` and import it in your contract:

   ```rust
   use stylus_cache_sdk::{is_contract_cacheable};
   ```

2. **Add the helper to your contract**

   ```rust
   /// Returns whether this contract is cacheable
   pub fn is_cacheable(&self) -> bool {
       is_contract_cacheable()
   }
   ```

3. **Deploy on Arbitrum Sepolia or Arbitrum One**

   Use the standard Stylus deploy command (replace the endpoint for mainnet):

   ```bash
   cargo stylus deploy --private-key "${PRIVATE_KEY}" --endpoint "https://sepolia-rollup.arbitrum.io/rpc"
   ```

## After Adding is_cacheable

Once your contract has `is_cacheable()` (as in `cached-contract/`):

1. **Deploy** with `cargo stylus deploy`:
   ```bash
   cd contracts/cached-contracts
   cargo stylus deploy --private-key "${PRIVATE_KEY}" --endpoint "https://sepolia-rollup.arbitrum.io/rpc"
   ```

2. **Cache check**: Running `cargo stylus deploy` will deploy and activate your contract. Use `is_cacheable()` to check whether the contract is cacheable:
   ```bash
   cast call <CONTRACT_ADDRESS> "is_cacheable()" --rpc-url <RPC_URL>
   ```

## Scripts (Windows CRLF)

If you see `bad interpreter` or line-ending errors on Windows, run:

```bash
pnpm fix-scripts
# or: dos2unix scripts/*.sh
# Install: apt install dos2unix  (Linux) | brew install dos2unix  (macOS)
```

## How SmartCache Works

SmartCache reduces gas costs and latency for Stylus contracts by **warming frequently-accessed storage slots** into a fast-access cache layer. When a contract opts in:

1. Storage reads that normally hit cold EVM slots (~2100 gas) are served from the warm cache (~100 gas)
2. Subsequent calls within the same block benefit from pre-warmed slots
3. Cross-contract calls to cached contracts also benefit from reduced access costs

> **Key insight**: SmartCache is **only applicable to Stylus contracts**. Solidity contracts and pre-deployed ERC20/ERC721 token contracts do not benefit because they lack the Stylus cache SDK hooks.

## Verifying Cache Status

After deploying, verify the cache is active:

### 1. Check if contract is cacheable

```bash
cast call <CONTRACT_ADDRESS> "is_cacheable()" --rpc-url <RPC_URL>
```

Expected output: `0x0000...0001` (true)

### 2. Compare gas usage (before vs after)

```bash
# Call a read function and check gasUsed
cast call <CONTRACT_ADDRESS> "get_count()" --rpc-url <RPC_URL> --trace

# After cache is warm, the same call should use less gas
```

### 4. Troubleshooting cache issues

| Symptom | Cause | Fix |
|---------|-------|-----|
| `is_cacheable()` returns false | Cache SDK not linked | Ensure `stylus-cache-sdk` is in Cargo.toml dependencies |
| No gas improvement | Cache not yet warm | Caching improves on repeated calls within a block or across consecutive blocks |

## Resources

- [SmartCache Documentation](https://smartcache.gitbook.io/smartcache-docs)
- [stylus-cache-sdk](https://crates.io/crates/stylus-cache-sdk)
- [Stylus Gas & Caching Deep Dive](https://docs.arbitrum.io/stylus/reference/opcode-gas-pricing)
