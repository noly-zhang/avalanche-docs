＃将松露与雪崩C链一起使用

##简介

[ Truffle Suite ]（https://www.trufflesuite.com）是用于在EVM上启动分散式应用程序\（dapps \）的工具包。使用Truffle，您可以编写和编译智能合约，构建工件，运行迁移并与已部署的合约进行交互。本教程说明了Truffle如何与Avalanche的C链（EVM的一个实例）一起使用。

##要求

您已经完成[运行雪崩节点]（../../ get-started.md），并且熟悉[雪崩的体系结构]（../../../ learn / platform-overview /）。您还通过[在X链和C链之间传输AVAX ]（../ platform / transfer-avax-between-x-chain-and-c-chain.md）教程执行了跨链交换获得资金到您的C-Chain地址。

##依赖关系

* [ Avash ]（https://github.com/ava-labs/avash）是用于运行本地Avalanche网络的工具。它类似于Truffle的[ Ganache ]（https://www.trufflesuite.com/ganache）。* [的NodeJS ]（https://nodejs.org/en）v8.9.4或更高版本。*松露，您可以用安装` NPM安装-g松露`



##启动本地雪崩网络

[Avash](https://github.com/ava-labs/avash) allows you to spin up private test network deployments with up to 15 AvalancheGo nodes out-of-the-box. Avash supports automation of regular tasks via lua scripts. This enables rapid testing against a wide variety of configurations. The first time you use avash you'll need to [install and build it](https://github.com/ava-labs/avash#quick-setup).

Start a local five node Avalanche network:

```text
cd /path/to/avash
# build Avash if you haven't done so
go build
# start Avash
./avash
# start a five node staking network
runscript scripts/five_node_staking.lua
```

A five node Avalanche network is running on your machine. When you want to exit Avash, run `exit`, but don't do that now, and don't close this terminal tab.

## Create truffle directory and install dependencies

Open a new terminal tab to so we can create a `truffle` directory and install some further dependencies.

First, navigate to the directory within which you intend to create your `truffle` working directory:

```text
cd /path/to/directory
```

Create and enter a new directory named `truffle`:

```text
mkdir truffle; cd truffle
```

Use `npm` to install [web3](https://web3js.readthedocs.io), which is a library through which we can talk to the EVM:

```text
npm install web3 -s
```

We'll use web3 to set an HTTP Provider which is how web3 will speak to the EVM. Lastly, create a boilerplace truffle project:

```text
truffle init
```

## Update truffle-config.js

One of the files created when you ran `truffle init` is `truffle-config.js`. Add the following to `truffle-config.js`.

```javascript
const Web3 = require('web3');
const protocol = "http";
const ip = "localhost";
const port = 9650;
module.exports = {
  networks: {
   development: {
     provider: function() {
      return new Web3.providers.HttpProvider(`${protocol}://${ip}:${port}/ext/bc/C/rpc`)
     },
     network_id: "*",
     gas: 3000000,
     gasPrice: 470000000000
   }
  }
};
```

Note that you can change the `protocol`, `ip` and `port` if you want to direct API calls to a different AvalancheGo node. Also note that we're setting the `gasPrice` and `gas` to the appropriate values for the Avalanche C-Chain.

## Add Storage.sol

In the `contracts` directory add a new file called `Storage.sol` and add the following block of code:

```text
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.8.0;

/**
 * @title Storage
 * @dev Store & retrieve value in a variable
 */
contract Storage {

    uint256 number;

    /**
     * @dev Store value in variable
     * @param num value to store
     */
    function store(uint256 num) public {
        number = num;
    }

    /**
     * @dev Return value 
     * @return value of 'number'
     */
    function retrieve() public view returns (uint256){
        return number;
    }
}
```

`Storage` is a solidity smart contract which lets us write a number to the blockchain via a `store` function and then read the number back from the blockchain via a `retrieve` function.

## Add new migration

Create a new file in the `migrations` directory named `2_deploy_contracts.js`, and add the following block of code. This handles deploying the `Storage` smart contract to the blockchain.

```javascript
const Storage = artifacts.require("Storage");

module.exports = function (deployer) {
  deployer.deploy(Storage);
};
```

## Compile Contracts with Truffle

Any time you make a change to `Storage.sol` you need to run `truffle compile`.

```text
truffle compile
```

You should see:

```text
Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/Storage.sol
> Artifacts written to /path/to/build/contracts
> Compiled successfully using:
   - solc: 0.5.16+commit.9c3226ce.Emscripten.clang
```

## Create, fund and unlock an account on the C-Chain

When deploying smart contracts to the C-Chain, truffle will default to the first available account provided by your C-Chain client as the `from` address used during migrations.

### Create an account

Truffle has a very useful [console](https://www.trufflesuite.com/docs/truffle/reference/truffle-commands#console) which we can use to interact with the blockchain and our contract. Open the console:

```text
truffle console --network development
```

Then, in the console, create the account:

```text
truffle(development)> let account = await web3.eth.personal.newAccount()
```

This returns:

```text
undefined
```

Print the account:

```text
truffle(development)> account
```

This prints the account:

```text
'0x090172CD36e9f4906Af17B2C36D662E69f162282'
```

### Unlock your account:

```text
truffle(development)> await web3.eth.personal.unlockAccount(account[0])
```

This returns:

```text
true
```

Exit the truffle console:

```text
truffle(development)> .exit
```

### Fund your account

Follow the steps in the [Transfer AVAX Between X-Chain and C-Chain](../platform/transfer-avax-between-x-chain-and-c-chain.md) tutorial to fund the newly created account. You'll need to send at least `135422040` nAVAX to the account to cover the cost of contract deployments.

## Run Migrations

Now everything is in place to run migrations and deploy the `Storage` contract:

```text
truffle migrate --network development
```

You should see:

```text
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.

Migrations dry-run (simulation)
===============================
> Network name:    'development-fork'
> Network id:      1
> Block gas limit: 99804786 (0x5f2e672)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > block number:        4
   > block timestamp:     1607734632
   > account:             0x34Cb796d4D6A3e7F41c4465C65b9056Fe2D3B8fD
   > balance:             1000.91683679
   > gas used:            176943 (0x2b32f)
   > gas price:           470 gwei
   > value sent:          0 ETH
   > total cost:          0.08316321 ETH

   -------------------------------------
   > Total cost:          0.08316321 ETH

2_deploy_contracts.js
=====================

   Deploying 'Storage'
   -------------------
   > block number:        6
   > block timestamp:     1607734633
   > account:             0x34Cb796d4D6A3e7F41c4465C65b9056Fe2D3B8fD
   > balance:             1000.8587791
   > gas used:            96189 (0x177bd)
   > gas price:           470 gwei
   > value sent:          0 ETH
   > total cost:          0.04520883 ETH

   -------------------------------------
   > Total cost:          0.04520883 ETH

Summary
=======
> Total deployments:   2
> Final cost:          0.13542204 ETH
```

If you didn't create an account on the C-Chain you'll see this error:

```text
Error: Expected parameter 'from' not passed to function.
```

If you didn't fund the account, you'll see this error:

```text
Error:  *** Deployment Failed ***

"Migrations" could not deploy due to insufficient funds
   * Account:  0x090172CD36e9f4906Af17B2C36D662E69f162282
   * Balance:  0 wei
   * Message:  sender doesn't have enough funds to send tx. The upfront cost is: 1410000000000000000 and the sender's account only has: 0
   * Try:
      + Using an adequately funded account
```

If you didn't unlock the account, you'll see this error:

```text
Error:  *** Deployment Failed ***

"Migrations" -- Returned error: authentication needed: password or unlock.
```

## Interacting with your contract

Now the `Storage` contract has been deployed. Let's write a number to the blockchain and then read it back. Open the truffle console again:

```text
truffle console --network development
```

Get an instance of the deployed `Storage` contract:

```javascript
truffle(development)> let instance = await Storage.deployed()
```

This returns:

```text
undefined
```

### Writing a number to the blockchain

Now that you have an instance of the `Storage` contract, call it's `store` method and pass in a number to write to the blockchain.

```javascript
truffle(development)> instance.store(1234)
```

If you see this error:

```text
Error: Returned error: authentication needed: password or unlock
```

Then run this again: `node web3_script.js`

You should see something like:

```javascript
{
  tx: '0x10afbc5e0b9fa0c1ef1d9ec3cdd673e7947bd8760b22b8cdfe08f27f3a93ef1e',
  receipt: {
    blockHash: '0x8bacbce7c9d835db524bb856288e3a73a6afbe49ab34abd8cd8826db0240eb21',
    blockNumber: 9,
    contractAddress: null,
    cumulativeGasUsed: 26458,
    from: '0x34cb796d4d6a3e7f41c4465c65b9056fe2d3b8fd',
    gasUsed: 26458,
    logs: [],
    logsBloom: '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
    status: true,
    to: '0x0d507b0467baef742f9cc0e671eddbdf6df41d33',
    transactionHash: '0x10afbc5e0b9fa0c1ef1d9ec3cdd673e7947bd8760b22b8cdfe08f27f3a93ef1e',
    transactionIndex: 0,
    rawLogs: []
  },
  logs: []
}
```

### Reading a number from the blockhain

To read the number from the blockchain, call the `retrieve` method of the `Storage` contract instance.

```javascript
truffle(development)> let i = await instance.retrieve()
```

This should return:

```javascript
undefined
```

调用的结果`检索`是` BN ` \（大数目\）。调用它的` .toNumber `方法来查看值：

javascript松露（开发）> i 。toNumber （）```



您应该看到存储的号码。

```的JavaScript 1234 ```



##摘要

现在，您拥有启动本地雪崩网络，创建松露项目以及创建，编译，部署和与Solidity合同进行交互所需的工具。

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM1MzI2NzM0N119
-->