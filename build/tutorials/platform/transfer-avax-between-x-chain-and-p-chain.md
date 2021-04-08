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

将为您提供**源链**和**目标链**的选择。分别选择X链和P链。您将看到X和P余额，以及用于输入要从源链转移到目标链的金额的输入字段。

！[发布图片]（../../../.gitbook/assets/wallet-x2p-05-xp.png）

输入您希望从X链转移到P链的金额。

###步骤5-确认交易

！[发布图片]（../../../.gitbook/assets/wallet-x2p-06-confirm.png）

按**确认**，然后**转移**发起转移。

###步骤6-完成！

跨链转移分为两个步骤：首先是从X链中导出资金的交易，然后是将其导入P链中的交易。钱包将同时执行这两项操作并显示其进度。

！[发布图片]（../../../.gitbook/assets/wallet-x2p-07-transfer.png）

而已！您已将AVAX从X链转移到P链！现在，您可以使用它们在Avalanche网络上进行验证或委派。

###从P链转移到X链

要将AVAX返回X链，您需要沿相反方向进行传输。

从** Source **和** Destination **下拉菜单中选择源链和目标链。其余过程相同：输入金额，确认并转账。

##通过API调用从X链转移到P链

如果要在Avalanche网络上构建应用程序，则可能希望通过编程方式进行传输，这是一些更广泛功能的一部分。您可以通过在AvalancheGo节点上调用适当的API来做到这一点。本教程的其余部分假定您可以访问AvalancheGo节点，X链上的AVAX令牌以及用户凭据[创建]（../../ avalanchego-apis / keystore-api.md＃keystorecreateuser）并存储在其中节点的密钥库。

下面所有的例子API调用假设节点是在本地运行\（即监听` 127.0.0.1 ` \）。该节点可以连接到主网络，测试网络或本地网络。在每种情况下，除了地址格式外，API调用和响应都应该相同。该节点不必是本地节点。您可以调用托管在其他位置的节点。

您可能已经注意到，使用雪崩钱包转移AVAX时，跨链转移是两个交易操作：

*出口AVAX从X连锁*导入AVAX至P-链


###步骤1-从X链导出AVAX

要导出AVAX，调用X链的[ ` avm.exportAVAX ` ]（../../ avalanchego的API / exchange-chain-x-chain-api.md＃AVM-exportavax）方法。

您的通话应如下所示：

```CPP
卷曲- X POST -数据“ { ”jsonrpc“ ：”2.0“ ，”ID“ ：1 ，”方法“ ：”avm.exportAVAX“ ，”PARAMS“ ：{ ”到“ ：“P-avax1wkmfja9ve3lt3n9ye4qp3l3gj9k2mz7ep45j7q “ ，” destinationChain“ ：” P“ ，” amount“ ：5000000 ，” changeAddr“ ：” X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8“ ，”用户名“：“ myUsername” ，“密码” ：        “ MYPASSWORD” }  }  '-H'内容-类型：应用/ JSON ; ' 127.0。0.1：9650  / EXT / BC / X ```其中`to`是P的地址-链地址您的用户控制和`changeAddr`是发送任何改变的地址。您可以将`changeAddr`留空；如果您将其保留为空白，则更改将返回到由您的用户控制的地址\ （请参见[此处] （ 。。/ 。。/ avalanchego -的API /平台-链- p -链- API 。MD＃平台- createaddress ） 用于创建一个说明新的P -链地址\ ）。请注意，你将支付交易费用为两个出口和进口业务。在此示例中，假设交易费用为` 。 001 `AVAX 。然后，上述出口实际上消耗了` 。006 `AVAX ; ` 。005 `去到P -链和` 。001`被烧成交易费。确保您发送的金额超过交易费用。否则，当您导入AVAX在P -链，它会消耗掉交易费用，并且你会在P _less_ AVAX结束-链。响应应看起来像这样： 
    
         
     
     
        
         
         
         
        

    







 

```的CPP {“jsonrpc”：“2.0”，“结果”：{“TxID添加添加”：“MqEaeWc4rfkw9fhRMuMTN7KUTNpFmh9Fd7KSre1ZqTsTQG73h”，“changeAddr”：“X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8”，}，“ID”：1} ```我们可以验证该交易被接受通过调用[`avm.getTxStatus`]（../../ avalanchego的API / exchange-chain-x-chain-api.md＃AVM-gettxstatus）：```CPP卷曲- X POST -数据“ { ”jsonrpc“ ：”2.0“ ，”方法“ ：”avm.getTxStatus“ ，”PARAMS“ ：{ ”TxID添加“ ：”MqEaeWc4rfkw9fhRMuMTN7KUTNpFmh9Fd7KSre1ZqTsTQG73h“ } ，“ id” ：



     
     
         
         
    
     







     
     
    
        
    
     1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/X
```

这表明我们的交易已被接受：

` ` ` cpp {“ jsonrpc”：“ 2.0”，“结果”：{“ status”：“ Accepted”}，“ id”：1} ` ”

     
     
         
    
     



我们也可以拨打[ ` avm.getBalance ` ]（../../ avalanchego的API / exchange-chain-x-chain-api.md＃AVM-为getBalance），以检查AVAX是从保持的地址扣除我们的用户：

```CPP
卷曲- X POST -数据“ { ”jsonrpc“ ：”2.0“ ，”ID“ ：1 ，”方法“ ：”avm.getBalance“ ，”PARAMS“ ：{ ”地址“ ：“X-ADDRESSGOESHERE “ ，”由assetid“ ：”AVAX“ } } '-H'内容-类型：应用/ JSON ; ' 127.0 。0.1 ：X ```扣除量导出的量\（` 0.005 ` AVAX在这个例子中\）加上交易费。如果您的用户控制多个X链地址，则可能是从它们的任意组合发送了AVAX。###步骤2-将AVAX导入P链我们的转帐尚未完成。我们需要调用P-链的[ ` platform.importAVAX ` ]（../../ avalanchego的API / platform-chain-p-chain-api.md＃平台importavax）方法来完成转移。您的通话应如下所示：```cppcurl -X POST --data '{"jsonrpc":"2.0","method":"platform.importAVAX","params":“到”{        :"P-avax1wkmfja9ve3lt3n9ye4qp3l3gj9k2mz7ep45j7q",        "sourceChain":"X",        "changeAddr":"P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u“，         ”密码“：” myPassword“，        ”用户名“：” myUsername“，    }，    “ id”：1 
    
         
     
     
        
        
    













     
     
     







}'-H'content-type：应用程序/ json;' 127.0.0.1:9650/ext/bc/P ```这将返回该事务ID：```的CPP {“jsonrpc”：“2.0”，“结果”：{“TxID添加添加”：“2sxo3ySETZ4xzXqAtgsUvZ5pdkqG4SML4c7Z7NoKLZcp77YNXC”，“ changeAddr”：“” P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u“”，” id“：1} ```我们可以通过以下方式检查交易是否被接受：```cpp curl - X POST - data' { “ jsonrpc” ：“ 2.0” ，“ id” ：1 ，“ method” ：“ avm.getTxStatus” ，“ params” ：{ “ txID” ："2sxo3ySETZ4xzXqAtgsUvZ5pdkqG4SML4c7Z7NoKLZcp77YNXC"}}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/ BC / X ```It should be `Committed`, meaning the transfer is complete. We can also check the balance of the address with:```CPP卷曲- X POST -数据“ { ”jsonrpc“ ：”2.0“ ，”方法“ ：”platform.{getBalance“，“ PARAMS”：“地址”：“ P- avax1wkmfja9ve3lt3n9ye4qp3l3gj9k2mz7ep45j7q” }






     
     
         
        
    
     







    
         
     
     
        
    






，“ ID”：1  }  '-H'内容-类型：应用/ JSON ；' 127.0。0.1：响应应看起来像这样： ```的cpp { “jsonrpc” ：“2.0” ，“结果” ：{ “平衡” ：“4000000” ，“utxoIDs” ：[ { “TxID添加” ：“2sxo3ySETZ4xzXqAtgsUvZ5pdkqG4SML4c7Z7NoKLZcp77YNXC ” ，0}]},"id":1}```Note that the balance we see is the amount exported from the X-Chain \(`.004` AVAX\) less the transaction fee \(`.001` AVAX in this example\). Now, we can use the AVAX held by 这个P -链地址，以验证主网提供的股份。##从P转移-链，X -连锁编程现在，让我们继续AVAX从P -链回X -链。和以前一样，这也是两个事务操作：*出口从P -链*
     
     
    
        
    
     







     
     
         
         
            
                 
                 
            
        
    
     



导入到X -连锁###步骤1 -从P出口AVAX -链要做到因此，请调用[ `platform 。exportAVAX` ] （。。/ 。。/ avalanchego -的API /平台-链- p -链- API 。 MD＃平台- exportavax ）： ```CPP卷曲- X POST -数据“ { ”jsonrpc“ ：“2.0 “ ，” method“ ：” platform.exportAVAX“ ，” params“ ：{ “到” ：“X-avax1fjn5rffqvny7uk3tjegjs6snwjs3hhgcpcxfax” ，“量” ：3000000 ，“changeAddr” ：“P-





 




 





     
     
     avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u”，“用户名”：“名为myUsername”，“密码”：“ MYPASSWORD” }，“ ID”：1  }  '-H'内容-类型：应用/ JSON ；' 127.0。0.1：其中`to`是X -的AVAX正被发送到链地址。这将返回事务ID ，并且我们可以检查该事务与其他呼叫致力于[ `平台。getTxStatus` ] （。。/ 。。/ avalanchego -
        
        
        
        
        
    
     





 的API /平台-链- p -链- API 。md＃platform - gettxstatus ）。同样，请确保您发送的金额超过交易费用。###步骤2 -导入AVAX到X -连锁完成我们从P转移-链的X -链，呼叫[ `AVM 。importAVAX` ] （。。/ 。。/

 

avalanchego -的API /交换-链- X -链- API 。md＃avm - importavax ）：```cpp curl - X POST - data' { “ jsonrpc” ：“ 2.0” ，“ id” ：1 ，“方法” ：“ avm.importAVAX” ，“ params” ：{ “到“ ：” X-avax1fjn5rffqvny7uk3tjegjs6snwjs3hhgcpcxfax“ ，” sourceChain“



    
         
     
     
        
        
        “changeAddr” ： “X-avax1turszjwn05lflpewurw96rfrd3h6x8flgs5uf8” ，“用户名” ：“名为myUsername” ，“密码” ：“MYPASSWORD” } } '-H'内容-类型：应用/ JSON ; ' 127.0 。0.1 ：9650 / EXT / BC / X ```
        
        
    



Note that `to` is the same address specified in our call to [`platform.exportAVAX`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-exportavax).

与以前一样，我们可以称之为[ ` avm.getBalance ` ]（../../ avalanchego的API / exchange-chain-x-chain-api.md＃AVM-为getBalance）来验证收到的资金。余额应该增加` 0.003 ` AVAX减去交易手续费。

##总结

而已！现在，您既可以使用雪崩钱包，也可以通过在雪崩节点上调用适当的API调用来在X链和P链之间来回交换AVAX。

现在，您可以使用P链上的令牌[在主网络上[将节点添加为验证器]（../nodes-and-staking/add-a-validator.md）。

<!--stackedit_data:
eyJoaXN0b3J5IjpbMjQ3ODM1OTQ2XX0=
-->