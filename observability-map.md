# Observability Map

## Objective
Map node runtime signals to system health indicators and safety relevance.

## Observability Architecture

### Signal Categories
1. **Logs**: Text-based event records
2. **Metrics**: Numerical measurements
3. **Traces**: Execution flow tracking
4. **State Queries**: On-demand state inspection

---

## Log Signals

### 1. Node Lifecycle Signals

#### Signal: Node Startup
```
INFO [timestamp] Starting Geth
INFO [timestamp] Maximum peer count: 50
INFO [timestamp] Allocated cache and file handles
```

**Meaning**: Node initialization in progress
**Safety Relevance**: CRITICAL - Indicates node is entering operational state
**Action**: Monitor for successful completion

---

#### Signal: Node Ready
```
INFO [timestamp] HTTP server started
INFO [timestamp] WebSocket server started
INFO [timestamp] IPC endpoint opened
```

**Meaning**: Node is ready to accept requests
**Safety Relevance**: HIGH - Node is operational
**Action**: Begin health checks

---

#### Signal: Node Shutdown
```
INFO [timestamp] Got interrupt, shutting down...
INFO [timestamp] HTTP server stopped
INFO [timestamp] Blockchain stopped
INFO [timestamp] Database closed
```

**Meaning**: Graceful shutdown in progress
**Safety Relevance**: CRITICAL - Ensures clean state persistence
**Action**: Wait for completion, verify clean exit

---

### 2. Blockchain Signals

#### Signal: Block Import
```
INFO [timestamp] Imported new chain segment
INFO [timestamp] blocks=1 txs=5 mgas=0.105 elapsed=10.234ms
INFO [timestamp] mgasps=10.263 number=12345 hash=0xabc123...
```

**Meaning**: New block successfully imported
**Safety Relevance**: HIGH - Chain progressing normally
**Action**: Monitor block rate, verify continuity

**Metrics Extracted**:
- Block number: 12345
- Transaction count: 5
- Gas used: 0.105 million gas
- Processing time: 10.234ms
- Throughput: 10.263 million gas per second

---

#### Signal: Block Mining
```
INFO [timestamp] Successfully sealed new block
INFO [timestamp] number=12346 sealhash=0xdef456... hash=0x789abc...
INFO [timestamp] elapsed=2.345s
```

**Meaning**: Node successfully mined/sealed block
**Safety Relevance**: HIGH - Block production operational
**Action**: Verify block propagation

---

#### Signal: Chain Reorganization
```
WARN [timestamp] Chain reorg detected
WARN [timestamp] number=12340 hash=0xold123... drop=5 dropfrom=0xold123...
WARN [timestamp] number=12340 hash=0xnew456... add=6 addfrom=0xnew456...
```

**Meaning**: Chain reorganization occurred (fork resolution)
**Safety Relevance**: CRITICAL - Chain state changed
**Action**: Verify new chain validity, check for deep reorgs

**Reorg Metrics**:
- Dropped blocks: 5
- Added blocks: 6
- Reorg depth: 5 blocks
- New canonical chain established

---

### 3. Synchronization Signals

#### Signal: Sync Started
```
INFO [timestamp] Block synchronization started
INFO [timestamp] Syncing: state download in progress
```

**Meaning**: Node is synchronizing with network
**Safety Relevance**: HIGH - Node catching up to network
**Action**: Monitor sync progress

---

#### Signal: Sync Progress
```
INFO [timestamp] Syncing: chain download in progress
INFO [timestamp] downloaded=1000 blocks, remaining=9000
INFO [timestamp] state entries=50000/500000
```

**Meaning**: Sync in progress with metrics
**Safety Relevance**: MEDIUM - Progress indicator
**Action**: Monitor for stalls

---

#### Signal: Sync Complete
```
INFO [timestamp] Block synchronization finished
INFO [timestamp] Fast sync complete, auto disabling
```

**Meaning**: Node fully synchronized
**Safety Relevance**: HIGH - Node ready for normal operation
**Action**: Begin transaction processing

---

### 4. Peer Network Signals

#### Signal: Peer Connected
```
INFO [timestamp] Peer connected
INFO [timestamp] id=abc123... name=Geth/v1.13.0 addr=192.168.1.100:30303
INFO [timestamp] peers=25
```

**Meaning**: New peer connection established
**Safety Relevance**: MEDIUM - Network connectivity
**Action**: Monitor peer count

---

#### Signal: Peer Disconnected
```
WARN [timestamp] Peer disconnected
WARN [timestamp] id=abc123... reason="disconnect requested"
INFO [timestamp] peers=24
```

**Meaning**: Peer connection lost
**Safety Relevance**: MEDIUM - Network change
**Action**: Monitor for mass disconnections

---

#### Signal: Peer Violation
```
WARN [timestamp] Peer sent invalid block
WARN [timestamp] Disconnecting peer: protocol violation
WARN [timestamp] Peer banned: id=abc123...
```

**Meaning**: Malicious or faulty peer detected
**Safety Relevance**: HIGH - Security event
**Action**: Log for analysis, verify peer banning

---

### 5. Transaction Pool Signals

#### Signal: Transaction Added
```
INFO [timestamp] Submitted transaction
INFO [timestamp] hash=0xtx123... from=0xabc... to=0xdef... value=1.0
```

**Meaning**: Transaction accepted to mempool
**Safety Relevance**: LOW - Normal operation
**Action**: Monitor for inclusion in block

---

#### Signal: Transaction Rejected
```
WARN [timestamp] Transaction rejected
WARN [timestamp] hash=0xtx456... reason="insufficient funds"
```

**Meaning**: Invalid transaction rejected
**Safety Relevance**: MEDIUM - Validation working
**Action**: Verify rejection reason valid

---

### 6. State Database Signals

#### Signal: State Commit
```
INFO [timestamp] Persisted trie from memory database
INFO [timestamp] nodes=1234 size=5.67MB time=123.45ms
```

**Meaning**: State changes written to disk
**Safety Relevance**: CRITICAL - State persistence
**Action**: Verify successful completion

---

#### Signal: Database Compaction
```
INFO [timestamp] Database compaction started
INFO [timestamp] Database compaction finished
INFO [timestamp] size reduction=15.3MB time=5.678s
```

**Meaning**: Database optimization in progress
**Safety Relevance**: MEDIUM - Performance maintenance
**Action**: Monitor for completion

---

### 7. Error Signals

#### Signal: Database Error
```
ERROR [timestamp] Database write failed
ERROR [timestamp] error="no space left on device"
```

**Meaning**: Critical database operation failed
**Safety Relevance**: CRITICAL - Data integrity risk
**Action**: IMMEDIATE - Free disk space, restart node

---

#### Signal: Consensus Error
```
ERROR [timestamp] Invalid block received
ERROR [timestamp] error="invalid state root"
```

**Meaning**: Consensus violation detected
**Safety Relevance**: CRITICAL - Protocol violation
**Action**: Verify block source, check for corruption

---

#### Signal: Network Error
```
ERROR [timestamp] Network unreachable
ERROR [timestamp] All peers disconnected
```

**Meaning**: Network connectivity lost
**Safety Relevance**: HIGH - Isolation risk
**Action**: Check network, verify connectivity

---

## Metrics Signals

### 1. Chain Metrics

#### Metric: Block Height
```javascript
eth.blockNumber
// Returns: 12345
```

**Meaning**: Current blockchain height
**Safety Relevance**: HIGH - Chain progress indicator
**Monitoring**: Track growth rate, detect stalls

---

#### Metric: Peer Count
```javascript
net.peerCount
// Returns: 25
```

**Meaning**: Number of connected peers
**Safety Relevance**: HIGH - Network health
**Monitoring**: Alert if < 5 peers (isolation risk)

---

#### Metric: Pending Transactions
```javascript
txpool.status
// Returns: {pending: 150, queued: 20}
```

**Meaning**: Transactions awaiting mining
**Safety Relevance**: MEDIUM - Mempool health
**Monitoring**: Alert if > 10000 (congestion)

---

#### Metric: Sync Status
```javascript
eth.syncing
// Returns: false (synced) or {currentBlock: 1000, highestBlock: 10000}
```

**Meaning**: Synchronization state
**Safety Relevance**: HIGH - Node readiness
**Monitoring**: Track sync progress, detect stalls

---

### 2. Performance Metrics

#### Metric: Gas Price
```javascript
eth.gasPrice
// Returns: 20000000000 (20 gwei)
```

**Meaning**: Current network gas price
**Safety Relevance**: LOW - Economic indicator
**Monitoring**: Track for transaction pricing

---

#### Metric: Block Gas Limit
```javascript
eth.getBlock("latest").gasLimit
// Returns: 8000000
```

**Meaning**: Maximum gas per block
**Safety Relevance**: MEDIUM - Capacity indicator
**Monitoring**: Track for capacity planning

---

#### Metric: Chain Difficulty
```javascript
eth.getBlock("latest").difficulty
// Returns: 12345678901234
```

**Meaning**: Current mining difficulty
**Safety Relevance**: MEDIUM - Network security indicator
**Monitoring**: Track for consensus health

---

### 3. Resource Metrics

#### Metric: Memory Usage
```javascript
debug.memStats()
// Returns: {Alloc: 500MB, TotalAlloc: 5GB, Sys: 600MB, ...}
```

**Meaning**: Node memory consumption
**Safety Relevance**: HIGH - Resource health
**Monitoring**: Alert if > 80% system memory

---

#### Metric: Disk Usage
```bash
# Database size
du -sh node-data/geth/chaindata
# Returns: 50GB
```

**Meaning**: Blockchain database size
**Safety Relevance**: HIGH - Storage capacity
**Monitoring**: Alert if > 90% disk capacity

---

#### Metric: CPU Usage
```bash
# Process CPU usage
top -p <geth-pid>
# Returns: 45% CPU
```

**Meaning**: Node CPU consumption
**Safety Relevance**: MEDIUM - Performance indicator
**Monitoring**: Alert if sustained > 90%

---

## State Query Signals

### 1. Account State
```javascript
eth.getBalance("0xabc123...")
// Returns: 1000000000000000000 (1 ETH)

eth.getTransactionCount("0xabc123...")
// Returns: 42 (nonce)

eth.getCode("0xcontract...")
// Returns: "0x608060405..." (contract bytecode)
```

**Meaning**: Current account state
**Safety Relevance**: HIGH - State integrity verification
**Usage**: Verify state consistency

---

### 2. Block State
```javascript
eth.getBlock(12345)
// Returns: {number, hash, parentHash, stateRoot, ...}
```

**Meaning**: Historical block data
**Safety Relevance**: HIGH - Chain integrity verification
**Usage**: Verify block immutability

---

### 3. Transaction State
```javascript
eth.getTransaction("0xtx123...")
// Returns: {hash, from, to, value, ...}

eth.getTransactionReceipt("0xtx123...")
// Returns: {status, gasUsed, logs, ...}
```

**Meaning**: Transaction execution result
**Safety Relevance**: HIGH - Execution verification
**Usage**: Verify transaction finality

---

## Observability Signal Matrix

| Signal Type | Source | Frequency | Safety Level | Action Required |
|-------------|--------|-----------|--------------|-----------------|
| Node Startup | Log | Once | CRITICAL | Monitor completion |
| Block Import | Log | Per block | HIGH | Track continuity |
| Chain Reorg | Log | Rare | CRITICAL | Investigate depth |
| Sync Progress | Log | Periodic | HIGH | Monitor stalls |
| Peer Connect | Log | Per peer | MEDIUM | Track count |
| Peer Violation | Log | Rare | HIGH | Log security event |
| Tx Rejected | Log | Per invalid tx | MEDIUM | Verify reason |
| State Commit | Log | Per block | CRITICAL | Verify success |
| DB Error | Log | On error | CRITICAL | IMMEDIATE action |
| Block Height | Metric | On-demand | HIGH | Track growth |
| Peer Count | Metric | On-demand | HIGH | Alert if low |
| Sync Status | Metric | On-demand | HIGH | Track progress |
| Memory Usage | Metric | Periodic | HIGH | Alert if high |
| Disk Usage | Metric | Periodic | HIGH | Alert if full |
| Account Balance | Query | On-demand | HIGH | Verify state |
| Block Data | Query | On-demand | HIGH | Verify integrity |

---

## Health Check Procedures

### 1. Startup Health Check
```bash
# Check node started
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":1}' http://localhost:8545

# Expected: {"jsonrpc":"2.0","id":1,"result":"1337"}
```

**Status**: ✓ Node responding

---

### 2. Sync Health Check
```bash
# Check sync status
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' http://localhost:8545

# Expected: {"jsonrpc":"2.0","id":1,"result":false}
```

**Status**: ✓ Node synced

---

### 3. Peer Health Check
```bash
# Check peer count
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}' http://localhost:8545

# Expected: {"jsonrpc":"2.0","id":1,"result":"0x19"} (25 peers)
```

**Status**: ✓ Peers connected

---

### 4. Block Production Health Check
```bash
# Check latest block timestamp
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false],"id":1}' http://localhost:8545

# Verify timestamp is recent (< 30 seconds old)
```

**Status**: ✓ Blocks being produced

---

## Alerting Rules

### Critical Alerts (Immediate Action)
1. **Node Crash**: Process terminated unexpectedly
2. **Database Error**: Write failures, corruption detected
3. **Deep Reorg**: Reorg depth > 10 blocks
4. **Peer Isolation**: Peer count = 0 for > 5 minutes
5. **Disk Full**: Disk usage > 95%

### High Priority Alerts (Action within 1 hour)
1. **Sync Stall**: No new blocks for > 10 minutes
2. **Low Peer Count**: Peers < 5
3. **Memory High**: Memory usage > 80%
4. **Consensus Violation**: Invalid blocks received

### Medium Priority Alerts (Action within 24 hours)
1. **Slow Block Import**: Block processing > 5 seconds
2. **High Mempool**: Pending transactions > 5000
3. **Peer Violations**: Multiple malicious peers detected

---

## Monitoring Dashboard

### Key Metrics Display
```
┌─────────────────────────────────────────┐
│ Node Status: OPERATIONAL                │
├─────────────────────────────────────────┤
│ Block Height: 12,345                    │
│ Sync Status: SYNCED                     │
│ Peer Count: 25                          │
│ Pending Txs: 150                        │
├─────────────────────────────────────────┤
│ Memory: 500MB / 8GB (6%)                │
│ Disk: 50GB / 500GB (10%)                │
│ CPU: 45%                                │
├─────────────────────────────────────────┤
│ Last Block: 5 seconds ago               │
│ Block Time: 12.3s avg                   │
│ Gas Price: 20 gwei                      │
└─────────────────────────────────────────┘
```

---

## Conclusion

**Observability: MAPPED**

Complete signal mapping established:
1. All log signals categorized and mapped
2. All metrics identified and monitored
3. State query capabilities documented
4. Health check procedures defined
5. Alerting rules established
6. Safety relevance assigned to each signal

**Operational Readiness**: Node observability is comprehensive, enabling:
- Real-time health monitoring
- Proactive issue detection
- Rapid incident response
- Performance optimization
- Security event tracking

**Next Verification**: Full deterministic reconstruction (see full-reconstruction-proof.md).
