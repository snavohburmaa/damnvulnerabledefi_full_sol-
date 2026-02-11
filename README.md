
## sol of 1. Unstoppable

**Suppose:**

- Vault has 100 tokens
- There are 100 shares
- **Everything matches**
- Each share = 1 token.
- Flash loans work normally.

**The Problem (How Someone Breaks It)**

Now imagine a random person sends tokens directly to the vault:

```solidity
token.transfer(vault, 10);
```

**IMPORTANT:** The vault did **NOT** mint new shares.

So now:

- Real tokens = 110
- Shares still represent = 100
- **Mismatch**

---

## 2. Naive Receiver

**Setup:** Pool has 1000 WETH (1 WETH fee per flash loan). Receiver has 10 WETH. Goal: move all to recovery in ≤ 2 txs.

**Bugs:**
1. Receiver only checks *caller* is the pool, not *who requested* the loan → anyone can call `flashLoan(receiver, ...)` and the receiver pays the fee. 10 calls with amount `0` → 10 × 1 WETH paid → receiver drained.
2. Pool uses a forwarder: “real” sender = signer of the request. Player can submit a request *signed by deployer* to `withdraw(1010 WETH, recovery)` → pool sends deployer’s balance to recovery.

**Solution:**  
- **Tx 1:** Deploy `Drainer`, call `drain(..., 10)` → 10 flash loans → receiver emptied.  
- **Tx 2:** Build forwarder request (deployer withdraws 1010 to recovery), sign with deployer’s key, call `forwarder.execute(req, signature)`.
