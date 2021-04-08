＃在X链和P链之间传输AVAX

##简介

AVAX令牌存在于X链上，可以在其中进行交易；在P链上，可以在验证主网络时将其作为股份提供；在C链上，可以在智能合约中使用它们。或支付汽油。Avalanche支持AVAX在这些链之间的移动，并且将来，Avalanche将支持链之间的更多通用原子交换。在本教程中，我们将在X链和P链之间发送AVAX令牌。

##要求

您已完成[入门]（../../ get-started.md），并熟悉[ Avalanche的体系结构]（../../../ learn / platform-overview /）。

为了发送AVAX，您需要安装一些AVAX！您可以通过在交易所购买来获得真实的AVAX，也可以从[ AVAX Test Faucet ]（https://faucet.avax-test.network）获得testnet AVAX ，这是一种免费，便捷的游戏方式与雪崩有关。

##使用网络钱包传输AVAX

在链之间传输AVAX的最简单方法是使用[ Avalanche Wallet ]（https://wallet.avax.network/），这是一种非托管且安全的访问和移动AVAX的方法。

可以在[此处]（https://github.com/ava-labs/avalanche-wallet）中找到Avalanche Wallet的源代码。

###步骤1-打开雪崩钱包

！[发布图片]（../../../.gitbook/assets/wallet-x2p-01-login.png）

选择**访问钱包**以输入您的钱包。要将钱包连接到除Avalanche主网络之外的其他网络，请选择** Mainnet **并选择要连接的网络。

###步骤2-登录到电子钱包

您可以使用私钥，助记词短语，密钥库文件或Ledger Nano S访问您的钱包。

！[发布图片]（../../../.gitbook/assets/wallet-x2p-02-access.png）

成功登录后，您将看到余额，资产组合和其他各种信息。

###步骤3-转到“交叉链”选项卡

！[发布图片]（../../../.gitbook/assets/wallet-x2p-03-earn.png）

功能为链之间传送令牌是在**跨链**标签。

###步骤4-输入转帐金额

You will be presented with a choice for **Source Chain** and **Destination Chain**. Select X-Chain and P-Chain, respectively. You will see your X and P balances, and an input field for entering the amount to transfer from source to destination chain.

![Image for post](../../../.gitbook/assets/wallet-x2p-05-x-p.png)

Enter the amount you wish to transfer from the X-Chain to the P-Chain.

### Step 5 - Confirm the Transaction

![Image for post](../../../.gitbook/assets/wallet-x2p-06-confirm.png)

Press **Confirm**, and then **Transfer** to initiate the transfer.

### Step 6 - Done!

A cross-chain transfer is a two step process: first a transaction to export the funds from the X-Chain, and another to import it to the P-Chain. The wallet will do both and show its the progress while doing so.

![Image for post](../../../.gitbook/assets/wallet-x2p-07-transfer.png)

That's it! You've transferred AVAX from the X-Chain to P-Chain! Now you can use them to validate or delegate on the Avalanche network.

### Transfer from P-Chain to X-Chain

To return the AVAX back to the X-Chain, you need to do the transfer in the opposite direction.

Swap the source and destination chains by selecting them from the **Source** and **Destination** drop-down menu. The rest of the process is the same: enter the amount, confirm and transfer.

## Transferring from the X-Chain to P-Chain with API Calls

If you're building an application on the Avalanche network, you may want to do the transfer programmatically as part of some broader functionality. You can do that by calling the appropriate APIs on an AvalancheGo node. The rest of the tutorial assumes you have access to an AvalancheGo node, AVAX tokens on the X-Chain, and user credentials [created](../../avalanchego-apis/keystore-api.md#keystorecreateuser) and stored in the node's keystore.

All the example API calls below assume the node is running locally \(that is, listening on `127.0.0.1`\). The node can be connected to the main network, a test network or a local network. In each case, the API calls and responses should be the same, except for the address formats. The node need not be local; you can make calls to a node hosted elsewhere.

As you may have noticed while transferring AVAX using the Avalanche Wallet, a cross-chain transfer is a two transaction operation:

* Export AVAX from the X-Chain
* Import AVAX to the P-chain

### Step 1 - Export AVAX from the X-Chain

To export AVAX, call the X-Chain’s [`avm.exportAVAX`](../../avalanchego-apis/exchange-chain-x-chain-api.md#avm-exportavax) method.

Your call should look like this:

```cpp
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"avm.exportAVAX",
    "params" :{
        "to":"P-avax1wkmfja9ve3lt3n9ye4qp3l3gj9k2mz7ep45j7q",
        "destinationChain": "P",
        "amount": 5000000,
        "changeAddr": "X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8",
        "username":"myUsername",
        "password":"myPassword"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

where `to` is the address of a P-Chain address your user controls and `changeAddr` is the address to send any change to. You can leave `changeAddr` blank; if you leave it blank, change will be returned to an address controlled by your user \(see [here](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-createaddress) for instructions on creating a new P-Chain address\).

Note that you will pay a transaction fee for both the export and import operations. In this example, let’s assume the transaction fee is `.001` AVAX. Then, the above export actually consumes `.006` AVAX; `.005` goes to the P-Chain and `.001` is burned as a transaction fee.

Make sure that the amount that you’re sending exceeds the transaction fee. Otherwise, when you import AVAX on the P-Chain, it will consume the transaction fee, and you’ll end up with _less_ AVAX on the P-Chain.

The response should look like this:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "txID": "MqEaeWc4rfkw9fhRMuMTN7KUTNpFmh9Fd7KSre1ZqTsTQG73h",
        "changeAddr": "X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8",
    },
    "id": 1
}
```

We can verify that this transaction was accepted by calling [`avm.getTxStatus`](../../avalanchego-apis/exchange-chain-x-chain-api.md#avm-gettxstatus):

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "avm.getTxStatus",
    "params":{
        "txID":"MqEaeWc4rfkw9fhRMuMTN7KUTNpFmh9Fd7KSre1ZqTsTQG73h"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

Which shows our transaction is accepted:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "status": "Accepted"
    },
    "id": 1
}
```

We can also call [`avm.getBalance`](../../avalanchego-apis/exchange-chain-x-chain-api.md#avm-getbalance) to check that the AVAX was deducted from an address held by our user:

```cpp
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"avm.getBalance",
    "params" :{
        "address":"X-ADDRESSGOESHERE",
        "assetID":"AVAX"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

The amount deducted is the exported amount \(`.005` AVAX in this example\) plus the transaction fee. If your user controls multiple X-Chain addresses, AVAX may have been sent from any combination of them.

### Step 2 - Import AVAX to the P-Chain

Our transfer isn’t done just yet. We need to call the P-Chain’s [`platform.importAVAX`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-importavax) method to finish the transfer.

Your call should look like this:

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.importAVAX",
    "params": {
        "to":"P-avax1wkmfja9ve3lt3n9ye4qp3l3gj9k2mz7ep45j7q",
        "sourceChain":"X",
        "changeAddr":"P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u",
        "username":"myUsername",
        "password":"myPassword",
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/P
```

This returns the transaction ID:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "txID": "2sxo3ySETZ4xzXqAtgsUvZ5pdkqG4SML4c7Z7NoKLZcp77YNXC",
        "changeAddr":"P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u"
    },
    "id": 1
}
```

We can check that the transaction was accepted with:

```cpp
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"avm.getTxStatus",
    "params" :{
        "txID":"2sxo3ySETZ4xzXqAtgsUvZ5pdkqG4SML4c7Z7NoKLZcp77YNXC"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

It should be `Committed`, meaning the transfer is complete. We can also check the balance of the address with:

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getBalance",
    "params":{
        "address":"P-avax1wkmfja9ve3lt3n9ye4qp3l3gj9k2mz7ep45j7q"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/P
```

The response should look like this:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "balance": "4000000",
        "utxoIDs": [
            {
                "txID": "2sxo3ySETZ4xzXqAtgsUvZ5pdkqG4SML4c7Z7NoKLZcp77YNXC",
                "outputIndex": 0
            }
        ]
    },
    "id": 1
}
```

Note that the balance we see is the amount exported from the X-Chain \(`.004` AVAX\) less the transaction fee \(`.001` AVAX in this example\). Now, we can use the AVAX held by this P-Chain address to provide a stake in order to validate the Primary Network.

## Transferring from the P-Chain to X-Chain programmatically

Now, let’s move AVAX from the P-Chain back to the X-Chain.

Same as before, this is also a two transaction operation:

* Export from the P-Chain
* Import to the X-Chain

### Step 1 - Export AVAX from the P-Chain

To do so, call [`platform.exportAVAX`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-exportavax):

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.exportAVAX",
    "params": {
        "to":"X-avax1fjn5rffqvny7uk3tjegjs6snwjs3hhgcpcxfax",
        "amount":3000000,
        "changeAddr":"P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u",
        "username":"myUsername",
        "password":"myPassword"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

where `to` is the X-Chain address the AVAX is being sent to.

This returns the transaction ID, and we can check that the transaction was committed with another call to [`platform.getTxStatus`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-gettxstatus). Again, make sure that the amount you’re sending exceeds the transaction fee.

### Step 2 - Import AVAX to X-Chain

To finish our transfer from the P-Chain to the X-Chain, call [`avm.importAVAX`](../../avalanchego-apis/exchange-chain-x-chain-api.md#avm-importavax):

```cpp
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"avm.importAVAX",
    "params" :{
        "to":"X-avax1fjn5rffqvny7uk3tjegjs6snwjs3hhgcpcxfax",
        "sourceChain":"P",
        "changeAddr": "X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8",
        "username":"myUsername",
        "password":"myPassword"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

Note that `to` is the same address specified in our call to [`platform.exportAVAX`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-exportavax).

与以前一样，我们可以称之为[ ` avm.getBalance ` ]（../../ avalanchego的API / exchange-chain-x-chain-api.md＃AVM-为getBalance）来验证收到的资金。余额应该增加` 0.003 ` AVAX减去交易手续费。

##总结

而已！现在，您既可以使用雪崩钱包，也可以通过在雪崩节点上调用适当的API调用来在X链和P链之间来回交换AVAX。

现在，您可以使用P链上的令牌[在主网络上[将节点添加为验证器]（../nodes-and-staking/add-a-validator.md）。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MzM5NDE2MjddfQ==
-->