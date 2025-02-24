# Abstruct

I don't want to discuss the specific incident, I just want to analyze the hacking incident from a technical perspective.

## Wallet with problems

Whenever you encounter such a hack, first look for the wallet in question. Exchange wallets are basically marked on etherscan, so just search for them.
<br>

![Bybit_Cold_Wallet](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/Bybit_Cold_Wallet.png)<br>

Look at this unlucky wallet, marked as Bybit cold wallet 1, but the money is only a pitiful twenty dollars. Most likely it is gone. Scroll down to see the historical transactions, as expected, 2 days ago, the money in this wallet was taken away like \"fallen leaves in the autumn wind, not a single hair was left\". ^-^
<br>

![withdrawn_Bybit_cold_wallet](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/withdrawn_Bybit_cold_wallet.png)<br>

## Crime process

Now that we have found the wallet in question, we can analyze the crime process. Firstly, let's take a look at the origin of this unlucky wallet.

<br>

![walletContract_Analysis](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/walletContract_Analysis.png)<br>

As can be seen from the above picture, first of all, it is a smart contract wallet, which means it has code logic. A closer look at the open source code logic reveals the keywords "proxy", "gnosis.io", so it is most likely a **Safe Wallet**.

Safe Wallet is a multi-signature wallet widely used for institutional fund management. Various cryptocurrency institutions either have a custodian, or if they manage their own accounts, it is usually a safe wallet.<br>

In addition to being used to directly store cryptocurrencies, it may also be used for various multi-signature operations, such as airdrops, upgrading contract codes, changing vault strategies, etc. Just understand that it is a very important wallet. (By the way, a few words to complain: The code has been on the chain for 7 or 8 years and has experienced the ups and downs of the cryptocurrency circle, and has never had any problems. It can be said to be the evergreen pine of Nanshan Mountain in the cryptocurrency circle.).

Knowing the wallet type, we can start analyzing transactions.

The names of the first few transactions are sweepERC20 sweepETH. Just looking at the names, you can tell that they are stealing money. Safe Wallet itself does not have these functions, so let’s ignore them for now.<br>

The transaction name at the bottom is **\"Exec Transaction\"**, which is a built-in function of the safe wallet. All transactions are conducted through it. This must be the starting point of this case. Let’s take a closer look.
<br>

![transactionHash](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/transactionHash.png)<br>

Look at **\"Overview\"** tag, exactly **\"Input Data\"**:
<br>

![execTransaction_InputData1](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/execTransaction_InputData1.png)<br><br>

![execTransaction_InputData2](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/execTransaction_InputData2.png)<br><br>

![execTransaction_InputData3](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/execTransaction_InputData3.png)<br><br>

![execTransaction_InputData4](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/execTransaction_InputData4.png)<br><br>

The transaction details are as follows:

```
0      to              address      0x96221423681A6d52E184D440a8eFCEbB105C7242
1      value           uint256      0
2      data            bytes     0xa9059cbb000000000000000000000000bdd077f651ebe7f7b3ce16fe5f2b025be29695160000000000000000000000000000000000000000000000000000000000000000
3      operation       uint8        1
4      safeTxGas       uint256      45746
5      baseGas         uint256      0
6      gasPrice        uint256      0
7      gasToken        address      0x0000000000000000000000000000000000000000
8      refundReceiver  address      0x0000000000000000000000000000000000000000
9      signatures      bytes 
 0xd0afef78a52fd504479dc2af3dc401334762cbd05609c7ac18db9ec5abf4a07a5cc09fc86efd3489707b89b0c729faed616459189cb50084f208d03b201b001f1f0f62ad358d6b319d3c1221d44456080068fe02ae5b1a39b4afb1e6721ca7f9903ac523a801533f265231cd35fc2dfddc3bd9a9563b51315cf9d5ff23dc6d2c221fdf9e4b878877a8dbeee951a4a31ddbf1d3b71e127d5eda44b4730030114baba52e06dd23da37cd2a07a6e84f9950db867374a0f77558f42adf4409bfd569673c1f
```

Firstly, check the **signatures** in detail.<br>

After looking at it for a long time, I think there is nothing wrong with it. This is a serious signature. This means that this transaction has indeed collected signatures from multiple wallet owners. You can think of it as a safe with multiple keys, and this transaction has indeed collected all the keys. Well, now at least we can be sure that the underlying contract of safe wallet is still safe. The hacker also managed to get these keys to open this safe wallet (How did they get these keys? We won't go into details for now.)

<hr>

## Hacker's Specific Operation

Then let’s take a look at the specific operations of this transaction and appreciate what the hacker did after opening the safe.<br>
The key point is this part:

```
0      to        address      0x96221423681A6d52E184D440a8eFCEbB105C7242
1      value     uint256      0
2      data      bytes        0xa9059cbb000000000000000000000000bdd077f651ebe7f7b3ce16fe5f2b025be29695160000000000000000000000000000000000000000000000000000000000000000
```

First, the value is 0, which means that this transaction did not transfer ETH. Considering that this is probably an operation to open a safe, and most of the deposits are interest-bearing assets **stETH**, that's normal.

**\"to\"** is a smart contract, and data is the content that interacts with this smart contract.
In brief, the method **\"0xa9059cbb\"** of the contract **\"0x96221423681A6d52E184D440a8eFCEbB105C7242\"** is called and the parameter **\"bdd077f651ebe7f7b3ce16fe5f2b025be2969516\"** is passed.<br>

So let's firstly take a look at what this **\"0x96221423681A6d52E184D440a8eFCEbB105C7242\"** contract is.
<br>

![ContractDeployedByHackers](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/ContractDeployedByHackers.png)<br>

I opened it and saw that it was not open source. It was probably a contract deployed by **hackers**.

Is this the end of the contract analysis? No, no, no, you must have thought of it. Even if it is not open source, we can find out what has been changed in this transaction.
<br>

![hackerChangesProxy](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/hackerChangesProxy.png)<br>

Let's take a closer look at the transaction. OK, we found the problem. This transaction changed the proxy pointing.

To explain briefly, the deployment of safe wallets uses proxy mode to save gas and facilitate upgrades. In simple terms, the wallet address used by users does not have code logic and only stores money. The hacker used this transaction to change the logic code pointed to by the wallet, causing this wallet to no longer be a safe wallet. It is just a safe wallet with its core being the code logic **\"bdd077f651ebe7f7b3ce16fe5f2b025be2969516\"** deployed by the hacker.

We can understand it by looking back at the sweep operations of the wallet. After the hacker took over the wallet, he ordered the wallet to spit out the money, and the wallet obeyed all the commands.
<br>

![transferSmallAmountOfUSDT](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/transferSmallAmountOfUSDT.png)<br><br>
![transfer_stETH](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/transfer_stETH.png)<br><br>
![transfer_mETH](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/transfer_mETH.png)<br><br>
![transfer_cmETH](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/transfer_cmETH.png)<br><br>
![transfer_cmETH_Details](https://github.com/wls503pl/BigEvents/blob/main/01_Bybit_Hacked_event_20250221/img/transfer_cmETH_Details.png)
