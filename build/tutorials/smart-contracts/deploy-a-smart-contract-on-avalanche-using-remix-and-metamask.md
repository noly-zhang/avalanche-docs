＃使用Remix和MetaMask在雪崩上部署智能合约

##简介

！[主要网络]（../../../.gitbook/assets/ primary- network.png）

雪崩的主网络是一个具有三个链的子网：P链，X链和C链。C链是由Avalanche的Snowman共识协议支持的以太坊虚拟机的实例。所述[ C-链RPC ]（../../ avalanchego的API / contract-chain-c-chain-api.md）可以通过使用复仇标准RPC调用做任何一个典型的客户机复仇罐。使用C链而不是以太坊的直接好处就是使用雪崩的所有好处。这些属性可以显着提高DApp的性能和用户体验。

今天，我们将使用Remix和MetaMask在Avalanche上部署和测试智能合约。

##步骤1：设置MetaMask

登录到MetaMask- ＆gt; 点击网络下拉菜单- ＆gt; 选择自定义RPC

！[metamask网络下拉菜单]（../../../ .gitbook / assets / metamask-network-dropdown.png）

#### ** Avalanche Mainnet设置：** 

* **网络名称**：雪崩主网C链* **新的RPC URL **：[ https://api.avax.network/ext/bc/C/rpc ]（https：//api.avax。网络/ EXT / BC / C / RPC）* ** ChainID **：` 0xa86a ` * **符号**：` AVAX ` * **资源管理器**：[ https://cchain.explorer.avax.network/ ]（https://cchain.explorer.avax.network/）





#### **FUJI Testnet Settings:**

* **Network Name**: Avalanche FUJI C-Chain
* **New RPC URL**: [https://api.avax-test.network/ext/bc/C/rpc](https://api.avax-test.network/ext/bc/C/rpc)
* **ChainID**: `0xa869`
* **Symbol**: `AVAX`
* **Explorer**: [https://cchain.explorer.avax-test.network](https://cchain.explorer.avax-test.network/)

#### **Local Testnet \(AVASH\) Settings:**

* **Network Name**: Avalanche Local
* **New RPC URL**:[ ](http://localhost:9650/ext/bc/C/rpc)[http://localhost:9650/ext/bc/C/rpc](http://localhost:9650/ext/bc/C/rpc)
* **ChainID**: `0xa868`
* **Symbol**: `AVAX`
* **Explorer**: N/A

## Step 2: Funding your C-Chain address

### **Using Avalanche Wallet**

On the main net, you can use the [Avalanche Wallet](https://wallet.avax.network/) to transfer funds from the X-Chain to your C-Chain address. The process is simple, as explained in this [tutorial](../platform/transfer-avax-between-x-chain-and-c-chain.md). Wallet can be used on test and local networks, too.

### **Using Test Network Faucet**

For funding on the test network, you can also use the Test Network Faucet. Navigate to [https://faucet.avax-test.network/](https://faucet.avax-test.network/) and paste your C-AVAX address. All you need to do is add a “C-” prefix and the faucet will switch from AVAX to C-AVAX.

### Funding on local testnet

On a local network, you can easily fund your addresses by deploying your own faucet. [Tutorial](https://medium.com/avalabs/the-ava-platform-tools-pt-2-the-ava-faucet-48f28da57146)

## Step 3: Connect MetaMask and deploy a smart contract using Remix

Open [Remix](https://remix.ethereum.org/) -&gt; Select Solidity

![remix file explorer](../../../.gitbook/assets/remix-file-explorer.png)

Load or create the smart contracts that we want to compile and deploy using Remix file explorer.

For this example, we will deploy an ERC20 contract from [OpenZeppelin](https://openzeppelin.com/contracts).

![ERC20 Contract](../../../.gitbook/assets/erc20-contract.png)

Navigate to Deploy Tab -&gt; Open the “ENVIRONMENT” drop-down and select Injected Web3 \(make sure MetaMask is loaded\)

![Deploy and run transactions](../../../.gitbook/assets/deploy-and-run-transactions.png)

Once we injected the web3-&gt; Go back to the compiler, and compile the selected contract -&gt; Navigate to Deploy Tab

![Solidity compiler](../../../.gitbook/assets/solidity-compiler.png)

Now, the smart contract is compiled, MetaMask is injected, and we are ready to deploy our ERC20. Click “Deploy.”

![Deploy erc20](../../../.gitbook/assets/deploy-erc20.png)

Confirm the transaction on the MetaMask pop up.

![Confirm ERC20](../../../.gitbook/assets/confirm-erc20.png)

Our contract is successfully deployed!

![Published metadata](../../../.gitbook/assets/published-metadata.png)

Now, we can expand it by selecting it from the “Deployed Contracts” tab and test it out.

![Interact with contract](../../../.gitbook/assets/interact-with-contract.png)

The contract ABI and Bytecode are available on the compiler tab.

![ABI bytecode](../../../.gitbook/assets/abi-bytecode.png)

如果您在阅读本教程时遇到任何困难，或者只是想与我们讨论雪崩，可以通过[ Discord ]（https://chat.avalabs.org/）加入我们的社区！

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA5MTkxOTc0M119
-->