＃创建一个虚拟机\（VM \）

_下面的代码有些过时了。一些方法，接口和实现与本教程中的稍有不同。由于当前代码非常相似，因此我们将不再赘述，并且本教程对于演示Avalanche的VM模型如何工作仍然很有用。_

##简介

Avalanche的核心功能之一是创建新的自定义区块链，这些区块链由[虚拟机\（VMs \） ]（../../../ learn / platform-overview /＃virtual-machines）定义

在本教程中，我们将创建一个非常简单的VM。VM定义的区块链是时间戳服务器。区块链中的每个块都包含创建时的时间戳以及32字节的数据\（payload \）。每个块的时间戳都在其父时间戳之后。

这样的服务器很有用，因为它可以用来证明在创建块时存在的一条数据。假设您有一本书的手稿，并且希望将来能够证明该手稿存在。您将一个块添加到区块链，其中该块的有效负载是您的手稿的哈希值。将来，您可以通过显示该块在其有效载荷中具有您的手稿的哈希来证明该手稿今天存在（这是由于无法找到哈希的原像这一事实\）。

Before we get to the implementation of the VM, we’ll look at the interface that a VM must implement to be compatible with the platform’s Avalanche consensus engine. We’ll show and explain all the code in snippets. If you want to see the code in one place, rather than in snippets, you can see it in our [GitHub repository.](https://github.com/ava-labs/avalanchego/tree/master/vms/timestampvm)

## The `snowman.VM` Interface

To reach consensus on linear blockchains \(as opposed to DAG blockchains\), Avalanche uses the Snowman consensus protocol. In order to be compatible with Snowman, the VM that defines the blockchain must implement the `snowman.VM` interface, which we include below from its declaration in[`github.com/ava-labs/avalanchego/blob/master/snow/engine/snowman/block/vm.go`](https://github.com/ava-labs/avalanchego/blob/master/snow/engine/snowman/block/vm.go).

The interface is big, but don’t worry, we’ll explain each method and see an implementation example. It’s not necessary you understand every nuance.

```cpp
// ChainVM defines the methods a Virtual Machine must implement to use the Snowman consensus engine.
//
// A Snowman VM defines the state contained in a linear blockchain,
// the state transition functions that modify the blockchain's state,
// the API exposed by the blockchain, as well as other aspects of the blockchain.
type ChainVM interface {
    // Initialize an instance of the blockchain defined by this VM.
    // [ctx]: Run-time context and metadata about the blockchain.
    //     [ctx.networkID]: The ID of the network this blockchain exists on.
    //     [ctx.chainID]: The unique ID of this blockchain.
    //     [ctx.Log]: Used to log messages
    //     [ctx.NodeID]: The ID of this node.
    // [db]: The database the blockchain persists data to.
    // [genesisBytes]: The byte representation of the genesis state of this blockchain.
    //                 If this VM were an account-based payments system, for example
    //                 `genesisBytes` would probably be a genesis
    //                 transaction that gives coins to some accounts, and this
    //                 transaction would be in the genesis block.
    // [toEngine]: The channel used to send messages to the consensus engine.
    // [fxs]: Feature extensions that attach to this VM.
    // In this release, we do not document feature extensions. You can ignore them.
    Initialize(
        ctx *snow.Context,
        db database.Database,
        genesisBytes []byte,
        toEngine chan<- Message,
        fxs []*Fx,
    ) error

    // Shutdown this blockchain.
    Shutdown()

    // Creates the HTTP handlers for this blockchain's API
    // and specifies the endpoint where they handle traffic.
    //
    // Each handler handles traffic to a specific endpoint.
    // Each endpoint begins with:
    // [Node's address]:[Node's HTTP port]/ext/bc/[blockchain ID]
    // A handler may handle traffic at an *extension* of the above endpoint.
    //
    // The method returns a mapping from an extension to the HTTP handler at that extension.
    //
    // For example, if this VM implements an account-based payments system,
    // CreateHandlers might return this map:
    // "accounts" --> [handler for API calls that pertain to accounts]
    // "transactions" --> [handler for API calls that pertain to transactions]
    //
    // The accounts handler would have endpoint [Node's address]:[Node's HTTP port]/ext/bc/[blockchain ID]/accounts
    // The trasnsactions handler would have endpoint [Node's address]:[Node's HTTP port]/ext/bc/[blockchain ID]/trasnsactions
    //
    // If a handler is mapped to by the empty string, it has no extension.
    // It handles traffic at [Node's address]:[Node's HTTP port]/ext/bc/[blockchain ID]
    CreateHandlers() map[string]*HTTPHandler

    // Attempt to create a new block from pending data in the blockchain's mempool.
    //
    // If there is no new block to be created, returns an error.
    BuildBlock() (snowman.Block, error)

    // Attempt to create a block from its byte representation.
    ParseBlock([]byte) (snowman.Block, error)

    // Attempt to fetch a block by its ID.
    //
    // If the block does not exist, returns an error.
    GetBlock(ids.ID) (snowman.Block, error)

    // Set the preferred block to the one with the specified ID.
    // New blocks will be built atop the preferred block.
    //
    // This should always be a block that has no children known to consensus.
    SetPreference(ids.ID)

    // LastAccepted returns the ID of the last accepted block.
    //
    // If no blocks have been accepted yet, should return the genesis block's ID.
    LastAccepted() ids.ID
}
```

## The snowman.Block Interface

You may have noticed the `snowman.Block` type referenced in the `snowman.VM` interface. It describes the methods that a block must implement to be a block in a linear \(Snowman\) chain.

Let’s look at this interface and its methods, which we copy from [`github.com/ava-labs/avalanchego/snow/consensus/snowman/block.go`.](https://github.com/ava-labs/avalanchego/blob/master/snow/consensus/snowman/block.go)

cpp //块是区块链中的一个块。// //保证按拓扑顺序验证，接受和拒绝块。具体来说，如果调用Verify，则父级已经//被验证。如果调用了“接受”，则父级已被接受。如果调用//拒绝，则表明父级已被接受或拒绝。// //如果块的状态为“未知”，则假定可以调用ID。//如果块的状态为“已接受”或“已拒绝”；父项，验证，接受和/或拒绝将永远不会被调用。type Block interface { // ID返回此块的唯一ID。//











    
    
    //通常，块的ID是其字节表示形式的哈希值。//重复调用时，块应返回相同的ID。ID （） ID 。ID //接受此阻止。// //网络中的每个正确节点都将接受此块。接受（）//拒绝此块。// //网络中任何正确的节点都不会接受此块。Reject （）//状态返回该块的当前状态。// //如果在具有该ID的n块上调用了Accept，则应该//返回Accepted 。同样，如果已使用此命令在块上调用了拒绝
    
    

    
    
    
    

    
    
    
    

    
    
    
    
    // ID，应返回已拒绝。如果此块的内容未知，则应返回未知。否则，应返回//处理。状态（）状态//父级返回此块的父级。// //如果父块未知，则应返回状态为Unknown的Block 。父（）块// //验证如果接受此块将进行的状态转换//是有效的。如果状态转换无效，则应该//返回非nil错误。// //确保已成功验证父项。验证（）
    
    
    

    
    
    
    
    

    
    
    
    
    
    错误//字节返回此块的二进制表示形式。// //用于将块发送给同级。//这些字节应该能够解析为另一个节点上的同一块。字节（）[ ]字节} ```

    
    
    
    
     



##库

我们创建了一些类型，您的VM实施可以嵌入\（嵌入类似于Go的继承版本\）以处理样板代码。

在我们的示例中，我们使用下面的两种库类型，建议您也使用它们。

### core.SnowmanVM

这种类型中，结构体，包含方法和字段共同到的所有实现` snowman.ChainVM `接口。

#### **Methods**

This type implements the following methods, which are part of the `snowman.ChainVM` interface:

* `SetPreference`
* `Shutdown`
* `LastAccepted`

If your VM implementation embeds a `core.SnowmanVM`, you do not need to implement any of these methods because they are already implemented by `core.SnowmanVM`. You may, if you want, override these inherited methods.

#### **Fields**

This type contains several fields that you’ll want to include in your VM implementation. Among them:

* `DB`: the blockchain’s database
* `Ctx`: the blockchain’s runtime context
* `preferred`: ID of the preferred block, which new blocks will be built on
* `lastAccepted`: ID of the most recently accepted block
* `toEngine`: the channel where messages are sent to the consensus protocol powering the blockchain
* `State`: used to persist data such as blocks \(can be used to put/get any bytes\)

### core.Block

This type, a struct, contains methods and fields common to all implementations of the `snowman.Block` interface.

#### **Methods**

This type implements the following methods, which are part of the `snowman.Block` interface:

* `ID`
* `Parent`
* `Accept`
* `Reject`
* `Status`

Your VM implementation will probably override `Accept` and `Reject` so that these methods cause application-specific state changes.

#### **Fields**

`core.Block` has a field VM, which is a reference to a `core.SnowmanVM`. This means that a `core.Block` has access to all of the fields and methods of that type.

## Timestamp Server Implementation

Now, we know the interface our VM must implement and the libraries we can use to build a VM.

让我们写我们的VM，它实现了` snowman.VM `，其块执行` snowman.Block `。

###块

首先，让我们看一下我们的块实现。

类型声明为：

cpp //块是链上的一个块。//每个块包含：// 1）一条数据（该块的有效载荷）// 2）创建该块时的（unix）时间戳记，类型为Block struct { * core 。块`serialize ：“ true” `    数据        [ 32 ]字节`serialize ：“ true” `    时间戳int64`serialize ：“ true” ` } ```




 
    





所述`的serialize：“真” `标签表示当块在数据库中被持久或发送到其它节点。带有标签的字段包含在序列化表示中。

#### **验证** 

cpp //如果该块有效，校验返回nil。//要有效，它必须是：// b.parent.Timestamp <b.Timestamp <= [本地时间] + 1小时func （ b * Block ）Verify （） error { //检查是否阻止已经通过调用// //嵌入的* core.Block进行验证。//如果检查时出错，则返回错误。//如果core.Block表示已接受该块，则返回接受。如果被接受，则错误：= b 。块。验证（）



  
    
    
    
    
    ; err ！= nil || accept { return err } //得到[b]的父母    parent ，好：= b 。父母（）。（*块）如果！好的{返回错误。新建（“从数据库检索块时出错” ）} //确保[b]的时间戳在其父时间戳之后。如果b 。时间戳<时间。的Unix
        
    

    

     
        
    

    
    （父级，时间戳， 0 ）。Unix （） {返回错误。新建（“块的时间戳比本地时间提前1个小时以上” ））} //确保[b]的时间戳不超过该节点时间的//小时（如果b）。时间戳> = time 。现在（）。加（时间。小时）。Unix （）{返回错误
        
    

    
    
     
        。New （“块的时间戳比本地时间早1小时以上” ））} //我们的块从* core.Block继承了VM。//它包含我们读取/写入的数据库b.VM.DB //我们使用VM的SaveBlock方法将该块持久保存到该数据库中。    b 。虚拟机。SaveBlock （ b 。 VM 。 DB ， b ）//然后刷新该数据库的内容返回b 。虚拟机。DB 。提交（）} ```
    

    
    
    


    
    



这就是我们块实现的所有代码！所有其他方法` snowman.Block '，这对我们'块'必须实现从继承` * core.Block `。

###虚拟机

现在，让我们来看看虚拟机的实现，它实现了` snowman.VM `接口。

声明是：

cpp //这个虚拟机定义了一个充当时间戳服务器的区块链//每个块都包含一块数据（有效载荷）和创建时的时间戳，类型为VM struct {     core 。SnowmanVM //编    解码器在字节编解码器codec中对结构进行序列化和反序列化。编解码器//尚未放入块中并尚未提出建议的    内存块[ ] [ 32 ] byte } ```


 


    


    




#### **初始化** 

cpp //初始化虚拟机// [ctx]是执行上下文// [db]是我们读/写的数据库// [toEngine]用于通知共识引擎新块已准备就绪//被添加到共识//在成因块中的数据是[genesisData] FUNC （ VM * VM ）初始化（     CTX *雪。上下文，     DB数据库。数据库，     genesisData [ ]字节，     toEngine瓒< -常见。消息，






  




     _ [ ] *常见的。Fx ，）错误{ //首先，我们初始化core.SnowmanVM。//我们将进一步介绍的vm.ParseBlock告诉core.SnowmanVM如果err ：= vm，如何从字节反序列化一个块。SnowmanVM 。初始化（ CTX ，分贝， VM 。 ParseBlock ， toEngine ）; err ！= nil {         ctx 。日志。

    
    
    
    
Error（“错误初始化SnowmanVM：％v”，err）        return err     }     //将vm的编解码器设置为新的编解码器，我们可以用来    //对块进行序列化和反序列化    vm.codec = codec.NewDefault（）    //如果数据库为空，    //    如果没有！vm.DBInitialized（）{         //请确保创世字节不超过32字节        // //使用创世数据初始化此区块链的状态//（创世区块与所有区块一样，保持32数据字节）











        if len （ genesisData ） >  32  {返回错误。New （“ genesis数据应该是字节（最大长度为32）” ）} // genesisData是一个字节片（因为那是snowman.VM接口所说的）//但每个块都包含一个字节数组。//要使类型匹配，请从genesisData获取头[[dataLen]个字节// //并将它们放入数组中        vargenesisDataArr [ dataLen ]字节副本（ genesisDataArr [ ：] ， genesisData ）//创建genesis块
            
        

        
        
        
        

        

        
        //创世块的时间戳为0。它没有父代，所以我们说父代的ID为空。//稍后我们将介绍NewBlock的定义。        genesisBlock ，错误：= vm 。NewBlock （ IDS 。清空， genesisDataArr ，时间。Unix的（0 ，0 ））如果ERR ！=零{             VM 。Ctx 。日志。错误（“创建创世块时出错：％v” ，错误
        
 
        
）return err } //将创世块保留到数据库中。//通常，在该块上调用Verify（）时，会将一个块保存到数据库中。//不过，我们不会在创世块上调用“验证”。（它没有父代，因此//它不会通过验证。）// vm.DB是数据库，是在初始化嵌入式SnowmanVM时设置的。如果err ：= vm 。SaveBlock （ VM 。 DB ， genesisBlock ）; err ！= nil {             vm 。Ctx 。日志。
            
        

        
        
        
        
        
        
错误（“保存创世块时出错：％v” ， err ）return err } //接受创世块。//将[vm.lastAccepted]和[vm.preferred]设置为genesisBlock。        genesisBlock 。Accept （）//将数据库标记为已初始化，以便将来在此链启动时//从数据库中拉出状态，而不是从创世        vm开始。SetDBInitialized （）//如果err ：= vm则刷新数据库。DB 。提交（
            
        

        
        


        
        


        
        ）; err ！= nil {
             vm 。Ctx 。日志。错误（“提交数据库时出错：％v” ， err ）return err } } return nil } ```
            
        
    
    



#### ** OffersBlock ** 

该方法将一条数据添加到内存池，并通知区块链的共识层已准备好构建新区块并对其进行投票。稍后我们将在何处调用它。

cpp // proposalBlock将[data]附加到[p.mempool]。//然后通知共识引擎//已准备好将新块添加到共识// //（即，具有数据[data]的块）func （ vm * VM ）proposalBlock （ data [ dataLen ] byte ）{     vm 。内存池=追加（ VM 。内存池，数据）     VM 。NotifyBlockReady （）} ```




   
 




#### ** ParseBlock **                                  

cpp // ParseBlock将[bytes]解析为一个雪人。Block // vm的状态使用此函数来解组保存在状态//中的块，//共识层在接收到块的字节表示时//来自另一个节点的func （ vm * VM ）ParseBlock （字节[ ]字节）（雪人。块，错误）{ //一个新的空块    block ：= ＆ Block { } //解组字节代表。块到我们的空块    错误：= vm 。编解码器。Unmarshal （字节，块）//初始化块//（块从其嵌入式* core.Block继承Initialize）    块。初始化（字节，＆ VM 。 SnowmanVM ）返回块， ERR } ```




    
    
 

    


    
    
 
    



#### ** NewBlock **                                  

cpp // NewBlock返回一个新的块，其中：//-块的父代具有ID [parentID] //-块的数据为[data] //-块的时间戳为[timestamp] func （ vm * VM ）NewBlock （的parentID IDS 。 ID ，数据[ DATALEN ]字节，时间戳时间。时间）（*块，错误）{ //创建我们新的块    的块：= ＆块{




    
    
 
        Block：     核心。NewBlock （ parentID ），
        数据：      数据，
        时间戳：时间戳。Unix （），} //获取块    blockBytes的字节表示， err ：= vm 。编解码器。如果err ！= nil则进行元帅（块）{ return nil ， err }
    

    

    
        
    

    // Initialize the block by providing it with its byte representation
    // and a reference to SnowmanVM
    block.Initialize(blockBytes, &vm.SnowmanVM)

    return block, nil
}
```

#### ** BuildBlock ** 

此方法的共识层应用层告诉它之后，一个新的块准备要建\调用（即，当` vm.NotifyConsensus（）`被称为\）。

```cpp
// BuildBlock returns a block that this VM wants to add to consensus
func (vm *VM) BuildBlock() (snowman.Block, error) {
    // There is no data to put in a new block
    if len(vm.mempool) == 0 { 
        return nil, errors.New("there is no block to propose")
    }

    // Get the value to put in the new block
    value := vm.mempool[0]
    vm.mempool = vm.mempool[1:]

    // Notify consensus engine that there are more pending data for blocks
    // (if that is the case) when done building this block
    if len(vm.mempool) > 0 {
        defer vm.NotifyBlockReady()
    }

    // Build the block
    block, err := vm.NewBlock(vm.Preferred(), value, time.Now())
    if err != nil {
        return nil, err
    }
    return block, nil
}
```

#### **CreateHandlers**

```cpp
// CreateHandlers returns a map where:
// Keys: The path extension for this blockchain's API (empty in this case)
// Values: The handler for the API
// In this case, our blockchain has only one API, which we name timestamp,
// and it has no path extension, so the API endpoint:
// [Node IP]/ext/bc/[this blockchain's ID]
// See API section in documentation for more information
func (vm *VM) CreateHandlers() map[string]*common.HTTPHandler {
    // Create the API handler (we'll see the declaration of Service further on)
    handler := vm.NewHandler("timestamp", &Service{vm})
    return map[string]*common.HTTPHandler{
        "": handler,
    }
}
```

### Service

AvalancheGo uses [Gorilla’s RPC library](https://www.gorillatoolkit.org/pkg/rpc) to implement APIs.

Using Gorilla, there is a struct for each API service. In the case of this blockchain, there’s only one API service.

The service struct’s declaration is:

```cpp
// Service is the API service for this VM
type Service struct{ vm *VM }
```

For each API method, there is: \* A struct that defines the method’s arguments \* A struct that defines the method’s return values \* A method that implements the API method, and is parameterized on the above 2 structs

#### **ProposeBlock**

This API method allows clients to add a block to the blockchain.

```cpp
// ProposeBlockArgs are the arguments to ProposeValue
type ProposeBlockArgs struct {
    // Data for the new block. Must be base 58 encoding (with checksum) of 32 bytes.
    Data string
}

// ProposeBlockReply is the reply from function ProposeBlock
type ProposeBlockReply struct{ 
    // True if the operation was successful
    Success bool
}

// ProposeBlock is an API method to propose a new block whose data is [args].Data.
func (s *Service) ProposeBlock(_ *http.Request, args *ProposeBlockArgs, reply *ProposeBlockReply) error {
    // Parse the data given as argument to bytes
    byteFormatter := formatting.CB58{}
    if err := byteFormatter.FromString(args.Data); err != nil {
        return errBadData
    }
    // Ensure the data is 32 bytes
    dataSlice := byteFormatter.Bytes
    if len(dataSlice) != 32 {
        return errBadData
    }
    // Convert the data from a byte slice to byte array
    var data [dataLen]byte             
    copy(data[:], dataSlice[:dataLen])
    // Invoke proposeBlock to trigger creation of block with this data
    s.vm.proposeBlock(data)
    reply.Success = true
    return nil
}
```

#### **GetBlock**

This API method allows clients to get a block by its ID.

```cpp
// APIBlock is the API representation of a block
type APIBlock struct {
    Timestamp int64  `json:"timestamp"` // Timestamp of most recent block
    Data      string `json:"data"`      // Data in the most recent block. Base 58 repr. of 5 bytes.
    ID        string `json:"id"`        // String repr. of ID of the most recent block
    ParentID  string `json:"parentID"`  // String repr. of ID of the most recent block's parent
}

// GetBlockArgs are the arguments to GetBlock
type GetBlockArgs struct {
    // ID of the block we're getting.
    // If left blank, gets the latest block
    ID string
}

// GetBlockReply is the reply from GetBlock
type GetBlockReply struct {
    APIBlock
}

// GetBlock gets the block whose ID is [args.ID]
// If [args.ID] is empty, get the latest block
func (s *Service) GetBlock(_ *http.Request, args *GetBlockArgs, reply *GetBlockReply) error {
    // If an ID is given, parse its string representation to an ids.ID
    // If no ID is given, ID becomes the ID of last accepted block
    var ID ids.ID
    var err error
    if args.ID == "" {
        ID = s.vm.LastAccepted()
    } else {
        ID, err = ids.FromString(args.ID)
        if err != nil {
            return errors.New("problem parsing ID")
        }
    }

    // Get the block from the database
    blockInterface, err := s.vm.GetBlock(ID)
    if err != nil {
        return errors.New("error getting data from database")
    }

    block, ok := blockInterface.(*Block)
    if !ok { // Should never happen but better to check than to panic
        return errors.New("error getting data from database")
    }

    // Fill out the response with the block's data
    reply.APIBlock.ID = block.ID().String()
    reply.APIBlock.Timestamp = block.Timestamp
    reply.APIBlock.ParentID = block.ParentID().String()
    byteFormatter := formatting.CB58{Bytes: block.Data[:]}
    reply.Data = byteFormatter.String()

    return nil
}
```

#### **API**

The resulting API has the following methods:

**timestamp.getBlock**

Get a block by its ID. If no ID is provided, get the latest block.

**Signature**

```cpp
timestamp.getBlock({id: string}) ->
    {
        id: string,
        data: string,
        timestamp: int,
        parentID: string
    }
```

* `id` is the ID of the block being retrieved. If omitted from arguments, gets the latest block
* `data` is the base 58 \(with checksum\) representation of the block’s 32 byte payload
* `timestamp` is the Unix timestamp when this block was created
* `parentID` is the block’s parent

**Example Call**

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "timestamp.getBlock",
    "params":{
        "id":"xqQV1jDnCXDxhfnNT7tDBcXeoH2jC3Hh7Pyv4GXE1z1hfup5K"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/timestamp
```

**Example Response**

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "timestamp": "1581717416",
        "data": "11111111111111111111111111111111LpoYY",
        "id": "xqQV1jDnCXDxhfnNT7tDBcXeoH2jC3Hh7Pyv4GXE1z1hfup5K",
        "parentID": "22XLgiM5dfCwTY9iZnVk8ZPuPe3aSrdVr5Dfrbxd3ejpJd7oef"
    },
    "id": 1
}
```

**timestamp.proposeBlock**

Propose the creation of a new block.

**Signature**

```cpp
timestamp.proposeBlock({data: string}) -> {success: bool}
```

* `data` is the base 58 \(with checksum\) representation of the proposed block’s 32 byte payload.

**Example Call**

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "timestamp.proposeBlock",
    "params":{
        "data":"SkB92YpWm4Q2iPnLGCuDPZPgUQMxajqQQuz91oi3xD984f8r"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/bc/timestamp
```

**Example Response**

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "Success": true
    },
    "id": 1
}
```

### Wrapping Up

That’s it! That’s the entire implementation of a VM which defines a blockchain-based timestamp server.

In this tutorial, we learned:

*该` snowman.ChainVM `接口，其中所有的VM定义的直链必须实现*的` snowman.Block `接口，其中所有的嵌段是由直链必须实现的部分*的` core.SnowmanVM `和`芯.Block `库类型，这使得虚拟机定义快



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTcwMzA0NDgwM119
-->