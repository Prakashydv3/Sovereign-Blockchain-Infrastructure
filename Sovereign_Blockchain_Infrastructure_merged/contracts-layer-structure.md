# Contracts Layer Structure

## Purpose
This layer contains the immutable anchor contract that provides deterministic anchoring for off-chain artifacts.

## Contract Role
The anchor contract serves as a minimal, immutable anchoring surface for:
- Registry snapshots
- Artifact hashes
- Timestamp proofs
- Parent-child linking

## Minimal Anchor Architecture

### Design Principle
The anchor contract maintains the smallest possible surface area to:
- Minimize attack vectors
- Prevent semantic drift
- Ensure long-term immutability
- Preserve deterministic verification

### Contract Surface
```
function anchor(bytes32 artifactHash, bytes32 parentHash) external
event Anchored(bytes32 indexed artifactHash, bytes32 parentHash, uint256 timestamp, address anchorer)
```

### Why Contract Surface Must Remain Minimal

1. **Immutability Requirement**
   - Once deployed, contract cannot be modified
   - Minimal surface = minimal future regret
   - No governance, no upgrades, no admin functions

2. **Deterministic Verification**
   - Simple contract = simple verification
   - Off-chain systems can reconstruct anchor chain
   - No complex state transitions to audit

3. **Attack Surface Reduction**
   - No token logic = no economic attacks
   - No access control = no privilege escalation
   - No external calls = no reentrancy risks

4. **Long-term Stability**
   - Minimal dependencies on blockchain state
   - Works across blockchain upgrades
   - Survives ecosystem changes

## Files in This Layer

### anchor_contract.sol
The immutable anchor contract implementation.

### anchor-interface.md
Public interface specification for off-chain integration.

### anchor-structure-spec.md
Technical specification of anchor data structures.

### anchor-parent-linking-proof.md
Proof that parent-child linking maintains chain integrity.

### anchor-non-mutation-proof.md
Proof that contract cannot be mutated post-deployment.

## Integration Points
- **Input**: Artifact hashes from registry snapshots
- **Output**: Anchored events with timestamps
- **Verification**: Off-chain systems reconstruct anchor chain from events

## Safety Guarantees
- No state mutation after anchor
- No admin privileges
- No upgrade mechanisms
- No external dependencies
- Deterministic event emission
