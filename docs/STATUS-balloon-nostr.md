# Status — balloon-nostr Track

**Last updated:** 2026-07-30
**Phase:** Extraction plan adopted from cross-track findings; wisp port complete

## MAJOR PIVOT — Direction Changed by Discovery Sync

balloon-hermes produced a comprehensive extraction plan (commit 041c231) that
redefines this track. Key finding: **NO WiFi on the balloon** — the flight path
is store-and-forward over LoRa/FIPS, NOT a WebSocket relay.

### What This Means
- wisp-esp32 C3 port = GROUND STATION reference (TollGate WiFi relay)
- Flight nostr = store-and-forward extracted FROM wisp into balloon-fresh
- Only 3 wisp modules are balloon-relevant: storage_engine, validator, sub_manager

### Already Done by balloon-hermes (in balloon-fresh master)
- [x] nostr_store rewritten to flash-backed design (10.4KB RAM, was 606KB) — commit 423e1f8
- [x] nostr_event_deserialize() implemented (was declared but undefined) — commit e3c1575
- [x] Merge into master — commit d2037c1
- [x] Extraction plan with memory feasibility analysis — commit 041c231

### Remaining Extraction Work (FROM wisp INTO balloon-fresh)
1. **storage_engine** — port with C3 caps (256 events vs 5000), LittleFS+NVS, query_events(filter), TTL sweeper, stats
2. **validator** — port config-driven pipeline, schema+id-hash always; sig verify PENDING secp256k1 C3 flash measurement
3. **sub_manager_match** — strip conn_fd/WS semantics, repurpose as LoRa pull matcher
4. **secp256k1 flash measurement** — gates decision on full Schnorr vs ground-station-deferred validation

## wisp-esp32 Port Status (Ground Station)

### Branches
- `balloon-nostr-extraction` — pushed to fork (felixfelix-bot/wisp-esp32)
- Latest: 41cf2a2 docs: discovery sync 2026-07-30

### Key Files Modified for C3 Port
- `main/main.c` — AP mode init, WiFi softAP config
- `main/router.c` — C3-compatible connection handling
- `sdkconfig` — C3 build config
- `partitions_c3.csv` — C3 partition table
- `sdkconfig.defaults` — C3 defaults

### Components (all functional in port)
- storage_engine (LittleFS persistence) — EXTRACTION SOURCE for flight
- sub_manager (subscription matching) — EXTRACTION SOURCE for flight
- validator (signature verification) — EXTRACTION SOURCE for flight
- broadcaster, deletion, rate_limiter, nip11, flash_monitor, ws_server — STAY in wisp (ground station only)

### Testing
- Native unit tests: `test/native/` (cmake, no device)
- Python test suite: connections, deletion, protocol, validation
- NOT YET: native compile verification, hardware flash test

### Blockers
None active. Board pending manufacture for hardware validation.

## Discovery Sync Log
- 2026-07-30: 67 findings. Critical: balloon-hermes nostr_store rewrite + extraction plan. PCB findings informational. All SPI/radio findings informational (relay has no SPI).
- 2026-07-30: 3 findings from circuit-design. Informational only. ESP32-C3 Mini V1 footprint confirmed on finalized PCB.

## Next Actions
1. Update INTEGRATION-ASSESSMENT.md with extraction plan adoption
2. When orchestrator assigns: execute remaining extraction items in balloon-fresh
3. Hardware flash test when board available (ground station validation)
