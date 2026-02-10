
## sol of 1. Unstoppable

**Suppose:**

- Vault has 100 tokens
- There are 100 shares
- **Everything matches** ✔️
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
- **Mismatch** ❌
