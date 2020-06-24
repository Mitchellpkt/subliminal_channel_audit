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

