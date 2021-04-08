＃使用雪崩钱包通过验证或委派来获取AVAX

## **介绍** <一个ID = “ 001F ” > </一>   

Avalanche钱包是一个基于Web的应用程序，没有中间件或任何类型的服务器通信。雪崩钱包是用Vue JS编写的，可以在线访问或编译并在本地运行。

可以在[此处]（https://wallet.avax.network/）访问雪崩钱包。  可以在[此处]（https://github.com/ava-labs/avalanche-wallet）中找到Avalanche Wallet的源代码。


**让我们开始下注！**

### **第1步-打开雪崩电子钱包** <一个ID = “ 552D ” > </一>   

！[发布图片]（https://miro.medium.com/max/1552/0*tpBIOjLdppuNKMjA）

您可以使用关键字短语，密钥库文件或Ledger Nano S \（即将推出！\）访问您的钱包。

### **第2步-导航至“赚取”部分** <一个ID = “ DC5A ” > </一>   

！[发布图片]（https://miro.medium.com/max/1504/0*XTh3nZzBI1bkLbwO）

**要投入资金，您需要在** [ **平台链\（P-Chain \）** ]（../../../ learn / platform-overview /＃platform-链-p-链）**！如果您的资金位于** [ **交换链\（X链\）** ]（../../../ learn / platform-overview /＃exchange-chain-x-chain）**，我们将需要通过发起跨链转移将它们转移到P链。如果您的令牌已被锁定，则它们已经在P链上，因此您无需执行从X链到P链的跨链转移。**  

！[发布图片]（https://miro.medium.com/max/1522/0*xKAf0nXSzqIdmBDg）

输入您想要转移到P链的金额，然后单击下面的“转移”按钮完成转移。

！[发布图片]（https://miro.medium.com/max/1488/0*aremeYNYtKP5nGPx）

这是！

！[发布图片]（https://miro.medium.com/max/1512/0*XP8f8CISy-LJ_Lc3）

现在，我们已经准备好资金用于在P-Chain上投注。接下来，您可以在钱包中添加验证器或委托人。

### **步骤3A：成为验证者！** <一个ID = “ 60f0 ” > </一>   

要添加验证器，我们需要运行一个节点。我们可以设置一个使用所释放的[二进制]（https://github.com/ava-labs/avalanchego/releases/）或者从建立他们[ AvalancheGo源代码（https://github.com/ava-实验室/ avalanchego）。

使用二进制文件既简单又方便，并通过以下四个步骤将您设置为验证者：

*下载[可在[此处]找到的最新版本tar.gz \（适用于osx和Windows的zip \）（https://github.com/ava-labs/avalanchego/releases）*解压到我们选择的文件夹中：


\ * Linux：tar -xvf avalanchego-linux- ＆lt; 版本＆gt; .tar.gz

\ * OSX：解压缩avalanchego-macos- ＆lt; 版本＆gt; 。压缩

\ * Windows：解压缩avalanchego-win- ＆lt; 版本＆gt; 。压缩

*导航到二进制文件目录cd avalanchego- ＆lt; 版本＆gt; *在Linux和OSX上使用./avalanchego和Windows上的AvalancheGo运行二进制文件


我们将让我们的节点引导并与其余节点通过网络同步，然后我们就可以开始滚动了。

我们将需要我们的节点ID。让我们发现使用[ info API ]（../../ avalanchego-apis / info-api.md）！

如果需要任何有关设置节点的帮助，请加入[ Discord ]（https://chat.avax.network/）。

！[发布图片]（https://miro.medium.com/max/1600/0*6hZSaT651Dd7R4bL）

填写字段并确认！

！[发布图片]（https://miro.medium.com/max/1600/0*cy61ZMDY5veMvCZj）

仔细检查详细信息，然后再次单击“确认”！

！[发布图片]（https://miro.medium.com/max/1600/0*f3GlN03He6TFkOV7）

恭喜你 您现在正在验证Avalanche主网络！

### **步骤3B：添加代理人！** <一个ID = “ 59bd ” > </一>   

！[发布图片]（https://miro.medium.com/max/1600/0*f-wXi2SiSm4eBmHt）

从活动的网络验证器列表中选择要用来委派令牌的验证器。

！[发布图片]（https://miro.medium.com/max/1600/0*uNnT2PtjCslRKFbF）

指定您的放样期和赌注金额。请注意所选验证器的结束时间。您的委派期限不能设置为超过验证者设置的结束日期。

！[发布图片]（https://miro.medium.com/max/1600/0*M_6_7L9jtYuPTp-A）

确认详细信息！

！[发布图片]（https://miro.medium.com/max/1600/0*Silj8-uZTm5g9xSi）

恭喜你 您现在正在委派Avalanche主网络！

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNDQ1MzQ1MjddfQ==
-->