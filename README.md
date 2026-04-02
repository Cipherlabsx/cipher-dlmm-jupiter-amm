# CipherDLMM Jupiter AMM Adapter

Jupiter AMM trait implementation for the Orbit Finance DLMM (CipherDLMM) on Solana.

## Program Details

| Field | Value |
|---|---|
| Program ID | `Fn3fA3fjsmpULNL7E9U79jKTe1KHxPtQeWdURCbJXCnM` |
| Program Name | Orbit Finance DLMM |
| Upgrade Authority | `orb4xgBF46t9VUUxoS7EcMyy6ytVVtUaNccz7YrEwqg` |
| Deploy Slot | 407,415,963 |
| DEX | [markets.cipherlabsx.com](https://markets.cipherlabsx.com) |

## What This Does

Implements Jupiter's `Amm` trait so that Jupiter's routing engine can include Orbit Finance DLMM pools in best-price calculations.

### Trait Methods Implemented

- `from_keyed_account()` — parse pool state from on-chain account data
- `get_accounts_to_update()` — return accounts Jupiter needs to fetch
- `update()` — refresh cached state from fetched accounts
- `quote()` — compute swap output from cached bin array state (no network calls)
- `get_swap_and_account_metas()` — build the swap instruction accounts

### Key Design Decisions

- **No network calls inside the trait.** Jupiter batch-fetches all accounts and delivers them via `update()`. The `quote()` function computes purely from cached bin array state.
- **Custom account layout.** Orbit Finance's account structure differs from Meteora's DLMM. This adapter handles the parsing natively.
- **Bin-based pricing.** Uses Q64.64 fixed-point math for price calculations across bins.

## Architecture

```
src/
├── lib.rs       — Amm trait implementation (entry point)
├── math.rs      — Bin arithmetic, Q64.64 pricing, swap simulation (21 KB)
├── accounts.rs  — Account struct definitions and parsing
├── state.rs     — Pool state, bin array, position types
└── pda.rs       — PDA derivation for pool accounts
```

## Building

```bash
cargo build
cargo test
```

## Dependencies

```toml
jupiter-amm-interface = "=0.6.0"
solana-sdk = "2.3"
solana-account-decoder = "2.2"
rust_decimal = "1"
ahash = "0.8"
bytemuck = "1"
uint = "0.10"
```

## Testing

24 tests covering all trait methods, edge cases, and bin traversal scenarios.

```bash
cargo test
```

## Security

- Original DLMM program audited by Hacken
- Re-audit in progress for new instructions added since initial audit
- This adapter is read-only — it computes quotes but does not modify on-chain state

## Links

- [Orbit Finance DEX](https://markets.cipherlabsx.com)
- [Documentation](https://docs.cipherlabsx.com)
- [GeckoTerminal](https://www.geckoterminal.com) (live)
- [DefiLlama](https://defillama.com/dexs/orbit-finance) (live)

## License

Apache-2.0
