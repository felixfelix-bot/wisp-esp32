# Status — balloon-nostr Track

**Last updated:** 2026-07-21

## Current Phase: Extraction

### Summary

wisp-esp32 ephemeral Nostr relay ported from ESP32-S3 to ESP32-C3 with AP mode support. Fork created at c03rad0r/wisp-esp32, working on branch balloon-nostr-extraction.

### What This Track Owns

Embedded Nostr relay for the balloon mesh stack. Provides:
- Ephemeral event storage (LittleFS, 21-day TTL)
- NIP-01 protocol (EVENT, REQ, CLOSE)
- NIP-09 deletion (event + address + kind-based)
- NIP-11 relay info document
- NIP-40 expiration
- Rate limiting
- Subscription fan-out (broadcaster)
- Flash storage monitoring + watchdog

### Branches
- `balloon-nostr-extraction` — active extraction branch (pushed to fork)
- `main` — upstream main (unchanged from extraction point)

### Commits (extraction branch)
- cecee37 docs: add anti-coordination guardrails to AGENTS.md
- 6065a26 feat: ESP32-C3 port with AP mode support
- (prior commits from upstream)

### Key Files Modified for C3 Port
- `main/main.c` — AP mode init, WiFi softAP config
- `main/router.c` — C3-compatible connection handling
- `sdkconfig` — C3 build config
- `partitions_c3.csv` — C3 partition table
- `sdkconfig.defaults` — C3 defaults

### Components
- storage_engine (LittleFS persistence)
- sub_manager (subscription matching)
- broadcaster (fan-out)
- deletion (NIP-09)
- validator (signature verification)
- rate_limiter
- nip11 (relay info)
- flash_monitor + watchdog
- ws_server (WebSocket)

### Testing
- Native unit tests: `test/native/` (builds with cmake, no device)
- Hardware integration tests: `test/hardware/integration_test.sh`
- Stress tests: `test/hardware/stress_test.sh`
- Python test suite: connections, deletion, protocol, validation

### Blockers
None.

### Discovery Sync — 2026-07-30
Received 3 findings from balloon-circuit-design (HARDWARE, PROTOCOL tags):
- hub_board_f33 PCB finalized: clearance-aware routing, GND mesh, 0 unconnected, Gerbers + JLCPCB order package ready
- Board uses ESP32-C3 Mini V1 footprint — matches my port target platform
- SPI0 pins defined: pads 3-6 (SCK/MOSI/MISO/NSS) routed to LR2021 module
- Impact on balloon-nostr: INFORMATIONAL ONLY. Relay is WiFi-only (WS server), uses no GPIO/SPI pins. Coexists with radio driver on same MCU. No firmware changes needed.
- Platform confirmation: ESP32-C3 Mini V1 hardware now manufacturing-ready.

### Next Actions
1. Run native test suite to verify C3 port compiles cleanly
2. Hardware flash test on ESP32-C3 (board pending manufacture)
3. Write INTEGRATION-ASSESSMENT.md for orchestrator review
