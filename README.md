
## sol of 1. Unstoppable

**Suppose:**

- Vault has 100 tokens
- There are 100 shares
- **Everything matches**
- Each share = 1 token.
- Flash loans work normally.

## sol of 2. Naive receiver

**Setup:** Pool has 1000 WETH (1 WETH fee per flash loan). Receiver has 10 WETH. Goal: get all funds to recovery in ≤ 2 txs.

**Bugs:**
1. Receiver only checks *caller* is the pool, not *who asked* for the loan. Anyone can call `flashLoan(receiver, ...)` and the receiver pays the fee. 10 calls with amount `0` → receiver pays 10 × 1 WETH → drained.
2. Pool uses a forwarder: “real” sender comes from signed request. Player can use a request *signed by deployer* to call `withdraw(1010 WETH, recovery)` → pool sends deployer’s balance to recovery.

**Solution:**  
- **Tx 1:** Deploy `Drainer`, call `drain(..., 10)` → 10 flash loans, receiver emptied.  
- **Tx 2:** Build forwarder request (deployer withdraws 1010 to recovery), sign with deployer’s key, call `forwarder.execute(req, signature)`.
