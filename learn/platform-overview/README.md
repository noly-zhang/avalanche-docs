---
描述：了解雪崩的核心概念和体系结构-


＃平台概述

雪崩具有3个内置的区块链：[ **交换链\（X-Chain \）** ]（.. ## exchange-chain-x-chain），[ **平台链\（P-Chain \）** ]（./# platform-chain-p-chain）和[ **合约链\（C链** \） ]（./# contract-chain-c-chain）。所有3个区块链均经过[验证]（http://support.avalabs.org/en/articles/4064704-what-is-a-blockchain-validator）并由[ **主网络** ]（http：/ /support.avalabs.org/en/articles/4135650-what-is-the-primary-network）。主网络是一个特殊的[子网]（http://support.avalabs.org/en/articles/4064861-what-is-a-subnetwork-subnet），并且所有自定义子网的所有成员还必须是以下成员的成员在主要网络中投放至少2,000 AVAX。

这是有关[创建子网]（../../ build / tutorials / platform / create-a-subnet.md）和[添加验证器]（../../ build / tutorials / nodes-and- staking / add-a-validator.md）添加到子网中。

！[主要网络]（../../.gitbook/assets/primary-network.png）

##子网

A **subnet**, or subnetwork, is a dynamic set of validators working together to achieve consensus on the state of a set of blockchains. Each blockchain is validated by exactly one subnet. A subnet can validate many blockchains. A node may be a member of many subnets.

A subnet manages its own membership, and it may require that its constituent validators have certain properties. This is very useful, and we explore its ramifications in more depth below:

### Compliance

Avalanche’s subnet architecture makes regulatory compliance manageable. As mentioned above, a subnet may require validators to meet a set of requirements.

Some examples include:

* Validators must be located in a given country
* Validators must pass a KYC/AML checks
* Validators must hold a certain license

### Support for Private Blockchains

You can create a subnet where only certain pre-defined validators may join and create a private subnet where the contents of the blockchains would be visible only to those validators. This is ideal for organizations interested in keeping their information private.

### Separation of Concerns

In a heterogeneous network of blockchains, some validators will not want to validate certain blockchains because they simply have no interest in those blockchains. The subnet model allows validators to only concern themselves with blockchains that they care about. This reduces the burden on validators.

### Application-Specific Requirements

Different blockchain-based applications may require validators to have certain properties. Suppose there is an application that requires large amounts of RAM or CPU power. A Subnet could require that validators meet certain [hardware requirements](http://support.avalabs.org/en/articles/4064879-technical-requirements-for-running-a-validator-node-on-avalanche) so that the application doesn’t suffer from low performance due to slow validators.

## Virtual Machines

A **Virtual Machine** \(VM\) defines the application-level logic of a blockchain. In technical terms, it specifies the blockchain’s state, state transition function, transactions, and the API through which users can interact with the blockchain. Every blockchain on Avalanche is an instance of a VM.

When you write a VM, you don't need to concern yourself with lower-level logic like networking, consensus, and the structure of the blockchain. Avalanche does this behind the scenes so you can focus on the thing you would like to build.

Think of a VM as a blueprint for a blockchain; you can use the same VM to create many blockchains, each of which follows the same ruleset but is logically independent of other blockchains.

### Why Virtual Machines?

At first, blockchain networks had one Virtual Machine \(VM\) with a pre-defined, static set of functionality. This rigid, monolithic design limited what blockchain-based applications one could run on such networks.

People who wanted custom decentralized applications had to create their own, entirely new blockchain network from scratch. Doing so required a great deal of time and effort, offered limited security, and generally resulted in a bespoke, fragile blockchain that never got off the ground.

Ethereum made a step toward solving this problem with smart contracts. Developers didn’t need to worry about networking and consensus, but creating decentralized applications was still hard. The Ethereum VM has low performance and imposes restrictions on smart contract developers. Solidity and the other few languages for writing Ethereum smart contracts are unfamiliar to most programmers.

Avalanche VMs \(AVMs\) make it easy to define a blockchain-based decentralized application. Rather than new, limited languages like Solidity, developers can write VMs in Go \(other languages will be supported in the future\).

### Creating Your Blockchain and Virtual Machine

Avalanche does not yet support the creation of new Virtual Machines \(VMs\). Presently, Avalanche only supports the creation of new instances of the Avalanche VM.

{% page-ref page="../../build/tutorials/platform/create-a-new-blockchain.md" %}

In the future, Avalanche will allow you to define and launch custom blockchains, and we’ll release SDKs to help you do so.

{% page-ref page="../../build/tutorials/platform/create-a-virtual-machine-vm.md" %}

## Exchange Chain \(X-Chain\)

The **X-Chain** acts as a decentralized platform for creating and trading digital smart assets, a representation of a real-world resource \(e.g., equity, bonds\) with a set of rules that govern its behavior, like “can’t be traded until tomorrow” or “can only be sent to US citizens.”

One asset traded on the X-Chain is AVAX. When you issue a transaction to a blockchain on Avalanche, you pay a fee denominated in AVAX.

The X-Chain is an instance of the Avalanche Virtual Machine \(AVM\). The [X-Chain API](../../build/avalanchego-apis/exchange-chain-x-chain-api.md) allows clients to create and trade assets on the X-Chain and other instances of the AVM.

{% page-ref page="../../build/tutorials/smart-digital-assets/create-a-fix-cap-asset.md" %}

## Platform Chain \(P-Chain\)

The **P-Chain** is the metadata blockchain on Avalanche and coordinates validators, keeps track of active subnets, and enables the creation of new subnets. The P-Chain implements the [Snowman consensus protocol](../../#snowman-consensus-protocol).

所述[ P-链API（../../构建/ avalanchego的API / platform-chain-p-chain-api.md）允许客户端创建的子网，添加验证器来的子网，并创建blockchains。

##合约链\（C链\）

在** C-链**允许使用创建智能合同[ C-链的API（/ avalanchego的API / contract-chain-c-chain-api.md ../../版本）。

C链是由[ Avalanche ]（../../）驱动的以太坊虚拟机的实例。

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQwODExMDQxN119
-->