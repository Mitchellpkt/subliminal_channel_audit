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
## Lock times (easy to execute, hard to notice)
Lock times less than 500,000,000 are interpreted as block heights, and _any lock time less than the current block height is ignored_ and can thus be used to embed arbitrary information wihtout impacting transaction behavior. Number of bits = log2(current_block_height)

Lock times greater than 500,000,000 are interpreted as unix epoch timestamps, so _any lock time between 500,000,000 and the current epoch time is ignored_ and can thus be used to embed arbitrary information wihtout impacting transaction behavior. Number of bits = log2(current_epoch_time - 500,000,000)

At time of writing, the monero blockchin height is 2127810 and the current tme is 1593020887. How much data can we pack into unlock_time without locking the transaction?

`# bits = log2(current_block_height) + log2(current_epoch_time - 500,000,000)`

`# bits = log2(2127810) + log2(1593020887 - 500,000,000)`

` ~ 51 bits` 

## Fees (easy to execute, hard to notice)
Monero allows a high degree of precision on plaintext fees, which is known to be [harmful for user privacy](https://github.com/monero-project/monero/issues/5711). Previous work has focused on presumably accidental information leaks in the most significant digits. Here, we examine the opposite, leveraging the least significant digits to intentionally encode data. 

Back of the envelope calculation: Let `typical_fee` be the current ballpark transaction fee. Users typically trust the dynamic fee calculations in their wallets, and are unlikely to check the transaction size and blockchain state by hand to notice or understand any discrepancies. Assume than any information encoding that impacts the fee value by less than some `notice_threshold` will go unnoticed by users (especially if including the slight margin over standard fees results in prompt mining! Nothing seems broken, so nothing to look for). A reasonable threshold is probably 10%, since I doubt users would notice that their wallet included a 0.000038275936 XMR fee when it should have included a 0.000035887352 XMR fee.

Currently a 2-in/2-out txn pays about 0.000030000000 fee, which is 30000000 atomic units.

and we'll use 10% as the notice_threshold.

`# bits = log2(typical_fee * notice_threshold)`

`# bits = log2(30000000 * 0.1)`

`~ 21 bits`

## Tx_extra (easy to execute)
Limited only by maximum transaction size. Lol.

## Vanity stealth addresses (hard to execute, easy to notice)
Unlike the above methods which are essentially computationally free, encoding information in stealth addresses / public keys requires partial collisions, which is a brute force attack. Number of bits depends on key generation time and another notice_threshold - I am unlikely to notice if transaction generation takes 2% longer to encode 4 bits of data, but I'll notice if it's 700% longer to encode a larger data payload.

## Ring member selection (esoteric)
For each ring of size N, one member is fixed by the true spend, and the other N-1 members are arbitrarily selected from a pool of (generally RingCT) outputs. The number of bits that could be encoded can be *approximated* as `log2((N-1)*(# RingCT outputs))`

Right now the ring size is N = 11, and there are on the order of 15000000 RingCT outputs on the chain. (?? double check number) Thus,

`# bits ~ log2((N-1)*(# RingCT outputs))`

`# bits ~ log2((11-1)*(15000000))`

`~ 27 bits`
