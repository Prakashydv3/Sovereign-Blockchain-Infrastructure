# Example Anchor Flow

## Purpose
This document demonstrates the complete flow of anchoring an off-chain artifact to the blockchain.

## Flow Overview

```
Registry Snapshot
    ↓
Artifact Hash Generation
    ↓
Anchor Contract Submission
    ↓
Anchor Event Emission
    ↓
Off-chain Verification
```

## Step-by-Step Flow

### Step 1: Registry Snapshot
Off-chain registry system creates a snapshot of its current state.

**Example Registry State:**
```json
{
  "registry_id": "sovereign-registry-v1",
  "snapshot_time": "2025-01-15T10:30:00Z",
  "artifacts": [
    {
      "artifact_id": "artifact-001",
      "content_hash": "0x1234...",
      "metadata": {...}
    }
  ]
}
```

### Step 2: Artifact Hash Generation
Registry computes cryptographic hash of the snapshot.

**Hash Computation:**
```
snapshot_json = serialize(registry_state)
artifact_hash = keccak256(snapshot_json)
```

**Example Output:**
```
artifact_hash = 0xa7b3c9d2e1f4567890abcdef1234567890abcdef1234567890abcdef12345678
```

### Step 3: Parent Hash Linking
System retrieves the previous anchor hash to create a chain.

**Parent Linking:**
```
previous_anchor = get_last_anchor()
parent_hash = previous_anchor.artifact_hash

// For first anchor:
parent_hash = 0x0000000000000000000000000000000000000000000000000000000000000000
```

### Step 4: Anchor Contract Submission
System submits anchor transaction to the blockchain.

**Contract Call:**
```solidity
anchor_contract.anchor(
    artifactHash: 0xa7b3c9d2e1f4567890abcdef1234567890abcdef1234567890abcdef12345678,
    parentHash: 0x0000000000000000000000000000000000000000000000000000000000000000
)
```

### Step 5: Anchor Event Emission
Contract emits event with anchor data.

**Event Structure:**
```solidity
event Anchored(
    bytes32 indexed artifactHash,
    bytes32 parentHash,
    uint256 timestamp,
    address anchorer
)
```

**Example Event:**
```json
{
  "event": "Anchored",
  "artifactHash": "0xa7b3c9d2e1f4567890abcdef1234567890abcdef1234567890abcdef12345678",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp": 1705315800,
  "anchorer": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "blockNumber": 12345678,
  "transactionHash": "0xabcd..."
}
```

### Step 6: Off-chain Verification
External systems verify the anchor chain.

**Verification Process:**
```
1. Retrieve all Anchored events from contract
2. Reconstruct anchor chain from events
3. Verify parent-child linking
4. Validate artifact hashes against registry snapshots
5. Confirm timestamp ordering
```

**Verification Code (Pseudocode):**
```javascript
function verifyAnchorChain(events) {
    let expectedParent = ZERO_HASH;
    
    for (event of events) {
        // Verify parent linking
        assert(event.parentHash === expectedParent);
        
        // Verify timestamp ordering
        assert(event.timestamp > previousTimestamp);
        
        // Update expected parent for next anchor
        expectedParent = event.artifactHash;
    }
    
    return true;
}
```

## Complete Example

### Example 1: First Anchor
```json
{
  "registry_snapshot": {
    "snapshot_id": "snap-001",
    "timestamp": "2025-01-15T10:30:00Z",
    "artifact_count": 42
  },
  "artifact_hash": "0xa7b3c9d2e1f4567890abcdef1234567890abcdef1234567890abcdef12345678",
  "parent_hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "anchor_event": {
    "blockNumber": 12345678,
    "timestamp": 1705315800,
    "transactionHash": "0xabcd1234..."
  }
}
```

### Example 2: Subsequent Anchor
```json
{
  "registry_snapshot": {
    "snapshot_id": "snap-002",
    "timestamp": "2025-01-15T11:30:00Z",
    "artifact_count": 45
  },
  "artifact_hash": "0xb8c4d3e2f1567890abcdef1234567890abcdef1234567890abcdef123456789a",
  "parent_hash": "0xa7b3c9d2e1f4567890abcdef1234567890abcdef1234567890abcdef12345678",
  "anchor_event": {
    "blockNumber": 12345890,
    "timestamp": 1705319400,
    "transactionHash": "0xdef5678..."
  }
}
```

## Anchor Chain Visualization

```
Genesis (parent = 0x000...)
    ↓
Anchor 1 (artifact = 0xa7b3..., parent = 0x000...)
    ↓
Anchor 2 (artifact = 0xb8c4..., parent = 0xa7b3...)
    ↓
Anchor 3 (artifact = 0xc9d5..., parent = 0xb8c4...)
    ↓
...
```

## Security Properties

### 1. Immutability
Once anchored, artifact hash cannot be changed.

### 2. Timestamp Authority
Blockchain provides trusted timestamp for each anchor.

### 3. Chain Integrity
Parent linking prevents insertion or reordering of anchors.

### 4. Public Verifiability
Anyone can reconstruct and verify the anchor chain.

## Integration Points

### Registry System
- Generates snapshots
- Computes artifact hashes
- Submits anchor transactions

### Anchor Contract
- Stores anchor data
- Emits anchor events
- Provides immutability guarantee

### Verification System
- Monitors anchor events
- Reconstructs anchor chain
- Validates integrity

## Example Data Location
See **examples/example-anchors.json** for complete anchor examples.

## Operational Procedures
See **node-operations/** for procedures to operate anchor infrastructure.

## Contract Specifications
See **contracts/** for anchor contract implementation and specifications.
