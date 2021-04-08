---
说明：>-  此页面列出了不推荐使用的API方法，参数和响应，这些API方法，参数和响应  在将来的版本中将被删除或修改。---




＃不推荐使用的API调用

## P链API

### ` getCurrentValidators `                                                                     

在v1.0.0中，签名为：

cpp
平台。getCurrentValidators （{ SUBNETID ：串} ） - > {    验证：[ ] {         STARTTIME ：串，        结束时间：串，         stakeAmount ：串，//可选        节点ID ：串，        重量：串，//可选        rewardOwner ：{             LOCKTIME ：

 


 

 
 
字符串，
            阈值：字符串，
            地址：字符串[ ] } ，         potentialReward ：字符串，        委托费用：字符串，        正常运行时间：字符串，        连接：布尔} ，    委托人：[ ] {         startTime ：字符串，         endTime ：字符串，        赌注数量：
        




    
 


串， //可选
        节点ID ：串，
         rewardOwner ： {
             LOCKTIME ：串，
            阈值：字符串，
            地址：串[ ] } ，         potentialReward ：串，} } ```
        

    



在更高版本中，签名如下。请注意，每个验证器均包含其委托人的列表。请查看下一个注释以了解当前行为。

cpp
平台。getCurrentValidators （{ SUBNETID ：串} ） - > {    验证：[ ] {         STARTTIME ：串，        结束时间：串，         stakeAmount ：串，//可选        节点ID ：串，        重量：串，//可选        rewardOwner ：{             LOCKTIME ：

 


 

 
 
 string,
            threshold: string,
            addresses: string[]
        },
        potentialReward: string,
        delegationFee: string,
        uptime: string,
        connected: boolean,
        delegators: []{
            startTime: string,
            endTime: string,
            stakeAmount: string, //optional
            nodeID: string,
            rewardOwner: {
                locktime: string,
                threshold: string,
                addresses: string[]
            },
            potentialReward: string,
        }
    },
    delegators: []{
        startTime: string,
        endTime: string,
        stakeAmount: string, //optional
        nodeID: string,
        rewardOwner: {
            locktime: string,
            threshold: string,
            addresses: string[]
        },
        potentialReward: string,
    }
}
```

Since v1.0.6, top level `delegators` field is removed. The signature is now:

```cpp
platform.getCurrentValidators({subnetID: string}) ->
{
    validators: []{
        startTime: string,
        endTime: string,
        stakeAmount: string, //optional
        nodeID: string,
        weight: string, //optional
        rewardOwner: {
            locktime: string,
            threshold: string,
            addresses: string[]
        },
        potentialReward: string,
        delegationFee: string,
        uptime: string,
        connected: boolean,
        delegators: []{
            startTime: string,
            endTime: string,
            stakeAmount: string, //optional
            nodeID: string,
            rewardOwner: {
                locktime: string,
                threshold: string,
                addresses: string[]
            },
            potentialReward: string,
        }
    }
}
```

### `getTxStatus`

Before v1.0.4, the signature was:

```cpp
platform.getTxStatus({txID: string} -> status: string
```

v1.0.4 added an argument `includeReason`. If `false` or not provided, this method's response was the same as before. If `true`, this method's response had this new format:

```cpp
{
  status: string,
  reason: string //optional
}
```

凡`理由'是交易中下跌的原因。`原因`只有存在，如果'状态'是' “丢弃” `。

由于1.0.6中，` includeReason `参数将被忽略，而且这种方法的反应总是在新的格式。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzOTMzMTA3MjRdfQ==
-->