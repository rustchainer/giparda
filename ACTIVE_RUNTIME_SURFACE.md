# Farm Attacker Active Runtime Surface

Frozen at commit `c72587e` on 2026-07-12.

## Active Strategy

Farm Attacker has one production strategy contour:

```text
scenario=dex_sniper_all_dex_external_sim
detection=dex_sniper
runtime_detector_bundle=BacktestStrategy
execution=state-based DEX entry/exit quote
wallet=SimWallet
```

The active scenario is:

```text
farm-attacker/trading_logic/scenarios/scenarios_all_dex_external.json
```

## Active Ownership

| Responsibility | Active owner |
| --- | --- |
| create-token/create-pool detection | `trading_logic/ingest` Dex Sniper detector |
| candidate runtime | `trading_logic/scenario_runtime` |
| DEX entry/exit quote math | `trading_logic/execution` |
| account-state quote caches | `trading_logic/execution/*_account_state_cache.rs` |
| simulated position/reserve accounting | `trading_logic/runtime/sim/wallet.rs` |
| first-window liquidity and seed events | `live/sec0.rs` |
| detector decisions | `trading_logic/strategy/backtest.rs` and `backtest/detectors` |
| causal replay | `stream_runtime_flow_bench::db_raw_runtime_conductor` |

The quote engine remains active under the neutral `ExecutionQuote*`,
`execution_quote`, `execution_entry_*`, `execution_exit_*`, and `[EXEC_QUOTE]`
contracts.

## Archived Legacy Surfaces

### Formula (Archived)

- Archive root:
  `archive/legacy_strategies/formula/`
- Preserved both formula contours, entry gate, multi-leg executor, trainers,
  replay tools, integration snapshots, tests, and documentation.
- Removed formula-derived targets from Sec0 and removed formula configuration,
  observers, entry waiting, and multi-leg state from the active runtime.

### CopyTrader (Archived)

- Archive root:
  `archive/legacy_strategies/copytrading/`
- Preserved detector, target-wallet state cache, mirrored buy/sell behavior,
  runtime exits, business logs, integration snapshots, tests, and docs.
- Removed from scenario config, detector registration, candidate transport,
  runtime branches, `tx_debug`, Cargo test discovery, and the active module
  graph.

The DEX quote engine under `trading_logic/execution/` is excluded from this
archive.

### Root Creators And Transfer Tracking (Archived)

- Archive root:
  `archive/legacy_strategies/root_creator/`
- Preserved RootChain/CreatorEdges detectors, transfer-hop graph, rootv2 tools
  and state, integration snapshots, tests, and docs.
- Removed from scenario schema, Farm Attacker startup, candidate DTO, stream
  routing, Yellowstone pre-copy retention, Cargo targets, and the active module
  graph.

## Completed Ownership Splits

1. `LiveDexSniperDetector` now has direct ownership in
   `ingest/dex_sniper_detector.rs`.
2. `CreateCandidate` now has direct ownership in
   `trading_logic/candidate.rs`.
3. Sec0 retains timing, liquidity, buyers, accounts, and seed events without
   carrying legacy exit targets.
4. Active quote contracts use neutral execution terminology; CopyTrader is
   archived without moving the shared DEX math.

## Cleanup Invariants

- No detector rule, purchase size, quote formula, reserve transition, exit
  decision, or PnL behavior changes as part of the cleanup.
- Historical quote-log tooling must continue to read old `copy_*` fields.
- Active Cargo/runtime code must not import archived sources.
- The running Farm Attacker process is not restarted before the frozen replay
  comparison passes.

## Frozen Baseline

The Phase 24 baseline is stored under:

```text
.planning/artifacts/phase24/baseline_c72587e/
```

The current commit already differs from the 2026-07-10 detector replay. That
pre-existing drift is recorded but is not attributed to Phase 24. Cleanup
acceptance compares against the fresh `c72587e` outputs, not against an older
binary.
