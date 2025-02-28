# Selling Stolen Goods

How to sell and launder the money stolen from the chain has always been a technical job. Here is analysis chart.

<br>
![]()<br>

First, let’s take a look at where the hacker spit out the money after instructing the wallet to spit it out.
Etherscan has been thoughtfully marked with **\"Bybit Exploiter 1\"**, and you can also see how much assets were stolen.

<br>
![]()<br>

The largest asset is the **stETH**, 90,000 of which were stolen. A simple calculation revealed that it didn’t match, as $1.5 billion was stolen from the wallet.
Looking back, I found that another transaction stole nearly 400,000 **ETH**, which was also spit out to **Bybit Exploiter 1**, so the number is correct now.

<br>
![]()<br>

OK, let’s continue reading and click on **Bybit Exploiter 1** to find out where the money went.

<br>
![]()<br>

I clicked in and saw that it was exactly what I expected. The first step in money laundering is to break it up.
The hacker methodically divided the ETH into 10,000 pieces and put them into various money laundering wallets, a total of 44 wallets.
Etherscan also thoughtfully marked.

We continued to track down and found that other stolen tokens, including **stETH**, **mETH**, etc., were exchanged for ETH in DEX by hackers.
It is obvious that hackers want to use ETH to launder money and sell stolen goods. So we just need to continue to track on ETH.

Let's pick a typical wallet.

<br>
![]()<br>

This wallet called **Bybit Exploiter 3** paid out the 10,000 stolen money in two installments. OK, it’s pretty simple, let’s continue.
Click on **Bybit Exploiter 54**, the money laundering operation has begun.

<br>
![]()<br>
![]()<br>

The money was divided into dozens of different parts and deposited into a bunch of wallets. This is the classic money laundering starter,
the flowers scattered by the goddess(The more scattered the money is, the harder it is to track. You can still track where a big rock rolls to,
but if it is ground into sand and enters the desert, it will really disappear).

We definitely can't track more than 80 transactions manually, so I use Python. Plus the support of cursor.

OK, let's first confirm that our goal is to track the final destination of the money, so we need a directed acyclic graph. **Neo4j** can be used to handle this.
Then we can roughly deduce where the money will go. In fact, there are three possible destinations for the money:
1. Centralized Exchanges
2. Cross-chain bridge
3. Coin mixer

First of all, it definitely can’t go to a big exchange, because CEXs are keeping an eye on the money, and once it enters the exchange,
it will be intercepted. So it can only go to a small exchange to try to sell the stolen assets.

As for cross-chain bridges and mixers, as long as they are doing business on the chain, they must be open source.
So our goal is to find the address of a small exchange and an open source contract.

That's it. The depth is not that great, and it reaches the bottom within 10. In other words, apart from transferring money back and forth,
the hacker has basically only made 10 effective operations on a single fund.

<br>
![]()<br>

The blue dot here is the marked Bybit Exploiter, which is the wallet where the initial stolen money entered. Let's select these points separately.

<br>
![]()<br>

Obviously, most of the nodes did not transfer funds outward, which is consistent with our previous judgment that only about 80 million US dollars were scattered among various addresses.

Looking at the picture, most of the points in the big picture are in a tree-like structure. This is how the hacker breaks the money down step by step.
However, there are some large clusters that are very eye-catching. You can investigate.

<br>
![]()<br>

Let's find the center points first. The center points are:
1. 0x963737c550e70ffe4d59464542a28604edb2ef9a (Hackers deposited 300 ETH and found that it was **unionchain.ai**, which seems to be a money laundering service)
2. 0xe8cb9e92adc6c61acbd317edff140d484366e512 (Hackers received 305.669 ETH)
3. 0xeba88149813bec1cccccfdb0dacefaaa5de94cb1 (The hacker deposited 34 ETH and found that it was an exchange **ChangeNow**)

First, the current balance of address 2 is 0, which clearly shows that address 2 is just a transit station.
There are a lot of funds in No.1 and No.3. Judging from past transactions, it is the small exchange we are looking for. After these two funds enter the exchange,
there is no way to track them. If these two exchanges do not freeze them, these two funds will be gone.

Next, let’s look for cross-chain bridges and mixers:
The green dots in the big picture are the open source contracts we detected. Let’s check them out.

<br>
![]()<br>

These smart contracts all have transfers from the hacker's address, and they are all open source, so they should be from legitimate projects on the chain.
Let's first drag out the list to match the contract address and contract name.

```
{"m.address": "0x20656e34b686b201b17cd9906eb74d24193ca607", "m.name":"Deposit"}
{"m.address": "0xfc99f58a8974a4bc36e60e2d490bb8d72899ee9f", "m.name":"TransparentUpgradeableProxy"}
{"m.address": "0x1231deb6f5749ef6ce6943a275a1d3e7486f4eae", "m.name":"LiFiDiamond"}
{"m.address": "0xba3cb449bd2b4adddbc894d8697f5170800eadec", "m.name":"CoWSwapEthFlow"}
{"m.address": "0x3fc91a3afd70395cd496c647d5a6cc9d4b2b7fad", "m.name":"UniversalRouter"}
{"m.address": "0xd37bbe5744d730a1d98d8dc97c42f0ca46ad7146", "m.name":"THORChain_Router"}
{"m.address": "0xe3985e6b61b814f7cdb188766562ba71b446b46d", "m.name":"MAYAChain_Router"}
{"m.address": "0x3c11f6265ddec22f4d049dde480615735f451646", "m.name":"Swapper"}
{"m.address": "0x66a9893cc07d91d95644aedd05d03f95e1dba8af", "m.name":"UniversalRouter"}
{"m.address": "0x14f5a892b912014874f88fe1ac6f473d8285a544", "m.name":"Deposit"}
{"m.address": "0x283b224fdc9e665b69918288f56379e59cb411d5", "m.name":"Deposit"}
{"m.address": "0x4c85f84803db4fec6fa29599b3c01bef2b9c8670", "m.name":"Deposit"}
{"m.address": "0xf1d9e3b45f12981683c90bb796c747f69bb52869", "m.name":"Deposit"}
{"m.address": "0x22ce84a7f86662b78e49c6ec9e51d60fdde7b70a", "m.name":"BKBridgeRouter"}
{"m.address": "0xf621fb08bbe51af70e7e0f4ea63496894166ff7f", "m.name":"MetaRouter"}
{"m.address": "0xf9a521579c09ac3addc2fcf7b705284e15b5e579", "m.name":"BridgersSwap"}
{"m.address": "0x00000047bb99ea4d791bb749d970de71ee0b1a34", "m.name":"TransitSwapRouterV5"}
```

It is obvious that some are cross-chain bridge projects and some are DEX projects.
