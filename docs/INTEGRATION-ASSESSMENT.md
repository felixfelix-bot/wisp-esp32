# Integration Assessment — balloon-nostr

**Track:** balloon-nostr
**Date:** 2026-07-21
**Assessment status:** DRAFT

## Component: wisp-esp32 (Embedded Nostr Relay)

### Purpose

Provides an ephemeral Nostr relay running on ESP32-C3 hardware. Acts as the message transport layer for balloon mesh nodes — events are stored locally, auto-expire after 21 days, and never touch third-party infrastructure.

### Hardware Target

- ESP32-C3 (4MB Flash, no PSRAM)
- AP mode (softAP) — relay broadcasts its own WiFi network
- Port: 4869 (WebSocket)

### NIP Compliance

| NIP | Status | Notes |
|-----|--------|-------|
| NIP-01 | Implemented | EVENT, REQ, CLOSE |
| NIP-09 | Implemented | Event + address + kind deletion |
| NIP-11 | Implemented | Relay info document served on HTTP GET |
| NIP-40 | Implemented | Expiration tags honored |

### Capacity (ESP32-C3, no PSRAM)

- ~1000 events (reduced from 5000 on S3 with PSRAM)
- 5-10 concurrent WebSocket connections
- 21-day TTL auto-purge

### Dependencies

- libsecp256k1 (Schnorr signature verification)
- libnostr-c (event parsing)
- noscrypt (NIP-44 crypto)
- cJSON (JSON parsing)
- LittleFS (flash persistence)

### Integration Points (Balloon Mesh)

This relay serves as:
1. Local event store for mesh nodes in range
2. Signing ceremony coordination point (FROST-compatible per upstream design)
3. Ephemeral message relay between balloon nodes

### Risks

1. **PSRAM absence on C3** — event index capped at ~1000. If mesh traffic exceeds this, older events evicted before TTL expiry. Mitigation: storage engine has compaction logic.
2. **Connection limits** — 5-10 concurrent WS connections. Sufficient for a single balloon cluster but may bottleneck if many nodes connect simultaneously.
3. **Power draw** — AP mode + WebSocket server increases power budget. Needs characterization for balloon power constraints.

### Test Coverage

- Native tests: router logic (no device required)
- Hardware integration: NIP-01 protocol round-trip, NIP-09 deletion, connection handling, validation
- Stress tests: connection flooding, event burst
- Untested on actual balloon hardware (pending)

### Outstanding Work

1. Verify C3 build compiles with ESP-IDF v5.3.4
2. Flash to physical ESP32-C3, run integration_test.sh
3. Characterize power draw in AP mode
4. Validate mesh integration (relay as transport between nodes)
