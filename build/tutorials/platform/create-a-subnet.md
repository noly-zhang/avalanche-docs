＃创建一个子网

##简介

阿[子网]（../../../学习/平台的概要/＃子网）是一组验证器。子网验证一组区块链。每个区块链仅由一个子网验证，该子网在区块链创建时指定。子网是一个强大的原语，它允许创建许可的区块链。

创建子网后，将指定阈值和一组密钥。\（实际上是指定密钥的地址，而不是密钥本身。\）为了向该子网添加验证器，需要这些密钥的_阈值_签名。我们将这些称为子网的**控制键**，并在将验证器添加到子网的交易中将控制键的签名称为**控制签名。**结果是子网可以控制其成员身份。

在本教程中，我们将创建一个具有2个控制键和2阈值的新子网。

###产生控制密钥<一个 ID = “生成最控制键” > </一>

首先，让我们生成2个控制键。要做到这一点，我们调用[ ` platform.createAddress ` ]（../../ avalanchego的API / platform-chain-p-chain-api.md＃平台createaddress）这产生了一个新的私有密钥并将其存储用户。

生成第一个密钥：

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.createAddress",
    "params": {
        "username":"USERNAME GOES HERE",
        "password":"PASSWORD GOES HERE"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

This gives the first control key \(again, it actually gives the _address_ of the first control key\). The key is held by the user we just specified.

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "address": "P-avax1spnextuw2kfzeucj0haf0e4e08jd4499gn0zwg"
    },
    "id": 1
}
```

Generate the second key:

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.createAddress",
    "params": {
        "username":"USERNAME GOES HERE",
        "password":"PASSWORD GOES HERE"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response contains the second control key, which is held by the user we just specified:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "address": "P-avax1zg5uhuwfrf5tv852zazmvm9cqratre588qm24z"
    },
    "id": 1
}
```

### Create the Subnet <a id="create-the-subnet"></a>

To create a subnet, we call [`platform.createSubnet`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-createsubnet).

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.createSubnet",
    "params": {
        "controlKeys":[
            "P-avax1spnextuw2kfzeucj0haf0e4e08jd4499gn0zwg",
            "P-avax1zg5uhuwfrf5tv852zazmvm9cqratre588qm24z"
        ],
        "threshold":2,
        "username":"USERNAME GOES HERE",
        "password":"PASSWORD GOES HERE"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response gives us the transaction’s ID, which is also the ID of the newly created Subnet.

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "txID": "3fbrm3z38NoDB4yMC3hg5pRvc72XqnAGiu7NgaEp1dwZ8AD9g",
        "changeAddr": "P-avax103y30cxeulkjfe3kwfnpt432ylmnxux8r73r8u"
    },
    "id": 1
}
```

### Verifying Success <a id="verifying-success"></a>

We can call [`platform.getSubnets`](../../avalanchego-apis/platform-chain-p-chain-api.md#platform-getsubnets) to get all Subnets that exist:

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "platform.getSubnets",
    "params": {},
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

The response confirms that our subnet was created:

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "subnets": [
            {
                "id": "3fbrm3z38NoDB4yMC3hg5pRvc72XqnAGiu7NgaEp1dwZ8AD9g",
                "controlKeys": [
                    "KNjXsaA1sZsaKCD1cd85YXauDuxshTes2",
                    "Aiz4eEt5xv9t4NCnAWaQJFNz5ABqLtJkR"
                ],
                "threshold": "2"
            }
        ]
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/P
```

###添加校验到子网<一个 ID = “附加验证到的子网” > </一>

本[教程]（../ nodes-and-staking / add-a-validator.md）将向您展示如何将验证器添加到子网中。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYxMTkzNzU4MV19
-->