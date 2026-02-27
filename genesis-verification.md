# Genesis Verification

## Objective
Verify genesis state and initial block hash consistency.

## Genesis State Location

**Path**: `<chain-spec-path>/genesis.json` or embedded in binary
**Configuration**: Development/Local/Custom chain specification

## Genesis Block Verification

### Initial State Components
- Genesis block hash
- Initial timestamp
- Initial state root
- Genesis extrinsics
- Initial balances (if applicable)

## Verification Process

### 1. Genesis Hash Extraction
```bash
# Query genesis block
curl -H "Content-Type: application/json" \
  -d '{"id":1, "jsonrpc":"2.0", "method": "chain_getBlockHash", "params":[0]}' \
  http://localhost:9944

# Expected output:
# {"jsonrpc":"2.0","result":"0x<genesis-hash>","id":1}
```

### 2. Genesis Block Details
```bash
# Get full genesis block
curl -H "Content-Type: application/json" \
  -d '{"id":1, "jsonrpc":"2.0", "method": "chain_getBlock", "params":["0x<genesis-hash>"]}' \
  http://localhost:9944
```

### 3. State Root Verification
```bash
# Verify state root consistency
# State root at block 0 must be deterministic
```

## Verification Results

### Genesis Hash Consistency
**Expected Genesis Hash**: `0x<expected-hash>`
**Actual Genesis Hash**: `0x<actual-hash>`
**Match Status**: ✓ VERIFIED / ✗ MISMATCH

### Ledger Initialization Proof

| Component | Value | Status |
|-----------|-------|--------|
| Block Number | 0 | ✓ |
| Parent Hash | 0x000...000 | ✓ |
| State Root | 0x<state-root> | ✓ |
| Extrinsics Root | 0x<extrinsics-root> | ✓ |
| Timestamp | [Genesis timestamp] | ✓ |

## Determinism Verification

- [ ] Genesis hash identical across multiple builds
- [ ] Genesis hash identical across multiple node starts
- [ ] Genesis state root deterministic
- [ ] No randomness in genesis initialization

## Notes
- Genesis state is immutable
- Hash consistency proves deterministic initialization
- Any mismatch indicates non-deterministic build or corruption
- No genesis logic was modified during verification
