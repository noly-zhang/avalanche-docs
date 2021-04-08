＃添加一个验证器

##简介

在[主要网络（https://avalanche.gitbook.io/avalanche/build/tutorials/platform/add-a-validator#introduction）是固有的雪崩平台和验证雪崩的[内置blockchains（HTTPS： //avalanche.gitbook.io/avalanche/learn/platform-overview）。在本教程中，我们将在主网络上添加一个节点，并在Avalanche上添加一个[子网]（https://avalanche.gitbook.io/avalanche/learn/platform-overview#subnets）。

P链管理雪崩上的元数据。这包括跟踪哪些节点位于哪些子网，哪些区块链存在以及哪些子网正在验证哪些区块链。要添加验证器，我们将向P链发布[交易]（http://support.avalabs.org/en/articles/4587384-what-is-a-transaction）。

{％hint style =“ danger”％}请注意，一旦发出事务以将节点添加为验证器，就无法更改参数。**您不能提早删除您的股份或更改股份金额，节点ID或奖励地址。**请确保您在下面的API调用中使用了正确的值。如果不确定，请浏览[开发者常见问题解答]（http://support.avalabs.org/en/collections/2618154-developer-faq）或寻求有关[ Discord。]（https://chat.avalabs.org/）{％endhint％}



##要求

您已经完成[运行雪崩节点]（../../ get-started.md），并且熟悉[雪崩的体系结构]（../../../ learn / platform-overview /）。在本教程中，我们使用[ Avalanche的Postman集合]（https://github.com/ava-labs/avalanche-postman-collection）帮助我们进行API调用。

为了确保您的节点是连接良好，请确保您的节点可以（接收和发送的跑马圈地端口上的TCP流量\ ` 9651 `默认\），以及您开始使用命令行参数的节点` --public- ip = [您的节点的公共IP在这里] `。如果不执行任何一项操作，可能会损害您的赌注。

##使用雪崩钱包添加验证器

首先，我们向您展示如何使用[ Avalanche Wallet ]（https://wallet.avax.network）将节点添加为验证器。

通过调用[获取您的节点的ID ` info.getNodeID ` ]（https://avalanche.gitbook.io/avalanche/build/apis/info-api#info-getnodeid） ：

！[getNodeID postman]（../../../ .gitbook / asset / getNodeID-postman.png）

```CPP
卷曲- X POST -数据' { “jsonrpc” ：“2.0” ，“ID” ：1 ，“方法” ：“info.getNodeID” } ' -H'内容-类型：应用/ JSON ; ' 127.0 。0.1 ：9650 /分机/信息```
    
         
     



该响应具有您节点的ID：

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "nodeID": "NodeID-5mb46qkSBj81k9g9e4VFjGGSbaaSLFRzD"
    },
    "id": 1
}
```

打开[钱包]（https://wallet.avax.network/），然后转到“赚取”标签。选择“添加验证器”。

！[网络钱包赚钱标签]（../../../.gitbook/assets/ web-wallet-earn- tab.png）

Fill out the staking parameters. They are explained in more detail below. When you’ve filled in all the staking parameters and double-checked them, click `Confirm`. Make sure the staking period is at least 2 weeks, the delegation fee rate is at least 2%, and you’re staking at least 2,000 AVAX.

{% page-ref page="../../../learn/platform-overview/staking.md" %}

![Earn validate](../../../.gitbook/assets/earn-validate.png)

You should see this success message, and your balance should be updated.

![Your validation transaction is sent](../../../.gitbook/assets/your-validation-transaction-is-sent.png)

Calling [`platform.getPendingValidators`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-getpendingvalidators) verifies that our transaction was accepted.

![getPendingValidators postman](../../../.gitbook/assets/getPendingValidators-postman.png)

Go back to the `Earn` tab, and click `Estimated Rewards`.

![Earn, validate, delegate](../../../.gitbook/assets/earn-validate-delegate.png)

Once your validator’s start time has passed, you will see the rewards it may earn, as well as its start time, end time, and the percentage of its validation period that has passed.

![Estimated rewards](../../../.gitbook/assets/estimated-rewards.png)

That’s it!

## Add a validator with API calls

We can also add a node to the validator set by making API calls to our node. To add a node the Primary Network, we’ll call [`platform.addValidator`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-addvalidator).

This method’s signature is:

```cpp
platform.addValidator(
    {
        nodeID: string,
        startTime: int,
        endTime: int,
        stakeAmount: int,
        rewardAddress: string,
        changeAddr: string, (optional)
        delegationFeeRate: float,
        username: string,
        password: string
    }
) -> {txID: string}
```

Let’s go through and examine these arguments.

`nodeID`

This is the node ID of the validator being added. To get your node’s ID, call [`info.getNodeID`](https://avalanche.gitbook.io/avalanche/build/apis/info-api#info-getnodeid):

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "info.getNodeID",
    "params":{},
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info
```

The response has your node’s ID:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "nodeID": "NodeID-LMUue2dBBRWdDbPL4Yx47Ps31noeewJji"
    },
    "id": 1
}
```

`startTime` and `endTime`

When one issues a transaction to join the Primary Network they specify the time they will enter \(start validating\) and leave \(stop validating.\) The minimum duration that one can validate the Primary Network is 24 hours, and the maximum duration is one year. One can re-enter the Primary Network after leaving, it’s just that the maximum _continuous_ duration is one year. `startTime` and `endTime` are the Unix times when your validator will start and stop validating the Primary Network, respectively. `startTime` must be in the future relative to the time the transaction is issued.

`stakeAmount`

In order to validate the Primary Network, one must stake AVAX. This parameter defines the amount of AVAX staked.

`rewardAddress`

When a validator stops validating the Primary Network, they will receive a reward if they are sufficiently responsive and correct while they validated the Primary Network. These tokens are sent to `rewardAddress`. The original stake will be sent back to an address controlled by `username`.

A validator’s stake is never slashed, regardless of their behavior; they will always receive their stake back when they’re done validating.

`changeAddr`

Any change resulting from this transaction will be sent to this address. You can leave this field empty; if you do, change will be sent to one of the addresses your user controls.

`delegationFeeRate`

Avalanche allows for delegation of stake. This parameter is the percent fee this validator charges when others delegate stake to them. For example, if `delegationFeeRate` is `1.2345` and someone delegates to this validator, then when the delegation period is over, 1.2345% of the reward goes to the validator and the rest goes to the delegator.

`username` and `password`

These parameters are the username and password of the user that pays the transaction fee, provides the staked AVAX, and to whom the staked AVAX will be returned.

Now let’s issue the transaction. We use the shell command `date` to compute the Unix time 10 minutes and 30 days in the future to use as the values of `startTime` and `endTime`, respectively. \(Note: If you’re on a Mac, replace `$(date` with `$(gdate`. If you don’t have `gdate` installed, do `brew install coreutils`.\) In this example we stake 2,000 AVAX \(2 x 1012 nAVAX\).

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.addValidator",
    "params": {
        "nodeID":"NodeID-LMUue2dBBRWdDbPL4Yx47Ps31noeewJji",
        "startTime":'$(date --date="10 minutes" +%s)',
        "endTime":'$(date --date="30 days" +%s)',
        "stakeAmount":2000000000000,
        "rewardAddress":"P-avax1d4wfwrfgu4dkkyq7dlhx0lt69y2hjkjeejnhca",
        "changeAddr": "P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u",
        "delegationFeeRate":10,
        "username":"USERNAME",
        "password":"PASSWORD"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response has the transaction ID, as well as the address the change went to.

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "txID": "6pb3mthunogehapzqmubmx6n38ii3lzytvdrxumovwkqftzls",
        "changeAddr": "P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u"
    },
    "id": 1
}
```

We can check the transaction’s status by calling [`platform.getTxStatus`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-gettxstatus):

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getTxStatus",
    "params": {
        "txID":"6pb3mthunogehapzqmubmx6n38ii3lzytvdrxumovwkqftzls"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The status should be `Committed`, meaning the transaction was successful. We can call [`platform.getPendingValidators`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-getpendingvalidators) and see that the node is now in the pending validator set for the Primary Network:

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getPendingValidators",
    "params": {},
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response should include the node we just added:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "validators": [
            {
                "nodeID": "NodeID-LMUue2dBBRWdDbPL4Yx47Ps31noeewJji",
                "startTime": "1584021450",
                "endtime": "1584121156",
                "stakeAmount": "2000000000000",
            }
        ] 
    },
    "id": 1
}
```

When the time reaches `1584021450`, this node will start validating the Primary Network. When it reaches `1584121156`, this node will stop validating the Primary Network. The staked AVAX will be returned to an address controlled by `username`, and the rewards, if any, will be given to `rewardAddress`.

## Adding a Subnet Validator

### Issuing a Subnet Validator Transaction

Now let’s add the same node to a subnet. The following will make more sense if you’ve already done this [tutorial on creating a Subnet](https://avalanche.gitbook.io/avalanche/build/tutorials/platform/create-a-subnet). Right now you can only add validators to subnets with API calls, not with Avalanche Wallet.

Suppose that the Subnet has ID `nTd2Q2nTLp8M9qv2VKHMdvYhtNWX7aTPa4SMEK7x7yJHbcWvr`, threshold 2, and that `username` holds at least 2 control keys.

To add the validator, we’ll call API method [`platform.addSubnetValidator`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-addsubnetvalidator). Its signature is:

```cpp
platform.addSubnetValidator(
    {
        nodeID: string,
        subnetID: string,
        startTime: int,
        endTime: int,
        weight: int,
        changeAddr: string, (optional)
        username: string,
        password: string
    }
) -> {txID: string}
```

Let’s examine the parameters:

`nodeID`

This is the node ID of the validator being added to the subnet. **This validator must validate the Primary Network for the entire duration that it validates this Subnet.**

`subnetID`

This is the ID of the subnet we’re adding a validator to.

`startTime` and `endTime`

Similar to above, these are the Unix times that the validator will start and stop validating the subnet. `startTime` must be at or after the time that the validator starts validating the Primary Network, and `endTime` must be at or before the time that the validator stops validating the Primary Network.

`weight`

This is the validator’s sampling weight for consensus. If the validator’s weight is 1 and the cumulative weight of all validators in the subnet is 100, then this validator will be included in about 1 in every 100 samples during consensus.

`changeAddr`

Any change resulting from this transaction will be sent to this address. You can leave this field empty; if you do, change will be sent to one of the addresses your user controls.

`username` and `password`

These parameters are the username and password of the user that pays the transaction fee. This user must hold a sufficient number of this Subnet’s control keys in order to add a validator to this Subnet.

We use the shell command `date` to compute the Unix time 10 minutes and 30 days in the future to use as the values of `startTime` and `endTime`, respectively. \(Note: If you’re on a Mac, replace `$(date` with `$(gdate`. If you don’t have `gdate` installed, do `brew install coreutils`.\)

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.addSubnetValidator",
    "params": {
        "nodeID":"NodeID-LMUue2dBBRWdDbPL4Yx47Ps31noeewJji",
        "subnetID":"nTd2Q2nTLp8M9qv2VKHMdvYhtNWX7aTPa4SMEK7x7yJHbcWvr",
        "startTime":'$(date --date="10 minutes" +%s)',
        "endTime":'$(date --date="30 days" +%s)',
        "weight":1,
        "changeAddr": "P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u",
        "username":"USERNAME",
        "password":"PASSWORD"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response has the transaction ID, as well as the address the change went to.

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "txID": "2exafyvRNSE5ehwjhafBVt6CTntot7DFjsZNcZ54GSxBbVLcCm",
        "changeAddr": "P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u"
    },
    "id": 1
}
```

We can check the transaction’s status by calling [`platform.getTxStatus`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-gettxstatus):

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getTxStatus",
    "params": {
        "txID":"2exafyvRNSE5ehwjhafBVt6CTntot7DFjsZNcZ54GSxBbVLcCm"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The status should be `Committed`, meaning the transaction was successful. We can call [`platform.getPendingValidators`](https://avalanche.gitbook.io/avalanche/build/apis/platform-chain-p-chain-api#platform-getpendingvalidators) and see that the node is now in the pending validator set for the Primary Network. This time, we specify the subnet ID:

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getPendingValidators",
    "params": {"subnetID":"nTd2Q2nTLp8M9qv2VKHMdvYhtNWX7aTPa4SMEK7x7yJHbcWvr"},
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response should include the node we just added:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "validators": [
            {
                "nodeID": "NodeID-LMUue2dBBRWdDbPL4Yx47Ps31noeewJji",
                "startTime":1584042912,
                "endTime":1584121156,
                "weight": "1"
            }
        ]
    },
    "id": 1
}
```

当时间到达' 1584042912 '，这个节点将开始验证该子网。当它到达' 1584121156 '，这个节点将停止验证该子网。

###将子网列入白名单

现在，已将节点添加为子网的验证器，让我们将其添加到子网的白名单中。白名单可防止节点无意间验证子网。

要将子网列入白名单，请重新启动节点，并将参数“ --whitelisted-subnets ”（以逗号分隔的子网列表）添加到白名单。

完整的命令是：

` ./build/avalanchego --whitelisted子网= nTd2Q2nTLp8M9qv2VKHMdvYhtNWX7aTPa4SMEK7x7yJHbcWvr `

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTUzMDk3NjI1XX0=
-->