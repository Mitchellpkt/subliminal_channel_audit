# Subliminal channel audit

### Research question 
How much information can a transaction generator hide in a Monero transaction without impacting its functionality? 

### Why do we care: 
We consider this threat vector in case compromised or closed-source wallets are embedding knowledge in users' transactions without their knowledge. This could be your local wallet smuggling information off your device, or exchanges/markets embedding additional information into their transactions. 

### Notes: 
The adversary performs only two actions:
1) Get users to install compromised wallets
2) Passively monitor all new transactions and scan for subliminal information

The adversary does NOT need to be the recipient in the transaction, any outside observer that knows the encoding patterns can extract the information.

**A malicious wallet can smuggle a LOT of information through a transaction**. There is also low-hanging fruit for fixing some of these channels without impacting protocol functionality. 

# Monero
## Lock times
Lock times less than 500,000,000 are interpreted as block heights, and _any lock time less than the current block height is ignored_ and can thus be used to embed arbitrary information wihtout impacting transaction behavior. Number of bits = log2(current_block_height)

Lock times greater than 500,000,000 are interpreted as unix epoch timestamps, so _any lock time between 500,000,000 and the current epoch time is ignored_ and can thus be used to embed arbitrary information wihtout impacting transaction behavior. Number of bits = log2(current_epoch_time - 500,000,000)

At time of writing, the monero blockchin height is 2127810 and the current tme is 1593020887. How much data can we pack into unlock_time without locking the transaction?

`log2(current_block_height) + log2(current_epoch_time - 500,000,000)`

`log2(2127810) + log2(1593020887 - 500,000,000)`

` ~ 51 bits` 
