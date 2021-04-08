＃管理员API

该API可用于测量节点的运行状况和调试。请注意，出于安全原因，默认情况下禁用Admin API。要运行一个节点与管理API使能，使用[命令行参数]（../引用/ command-line-interface.md）` --api管理员启用=真`。 

##格式

该API使用` JSON 2.0 ` RPC格式。

{％page-ref page =“ isinging-api-calls.md”％}

##端点



































#### **Example Response**

```text
{
    "jsonrpc":"2.0",
    "id"     :1,
    "result" :{
        "success":true
    }
}
```

Now, calls to the X-Chain can be made to either `/ext/bc/X` or, equivalently, to `/ext/myAlias`.

### admin.aliasChain

Give a blockchain an alias, a different name that can be used any place the blockchain’s ID is used.

#### **Signature**

```text
admin.aliasChain(
    {
        chain:string,
        alias:string
    }
) -> {success:bool}
```

* `chain` is the blockchain’s ID.
* `alias` can now be used in place of the blockchain’s ID \(in API endpoints, for example.\)

#### **Example Call**

```text
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"admin.aliasChain",
    "params": {
        "chain":"sV6o671RtkGBcno1FiaDbVcFv2sG5aVXMZYzKdP4VQAWmJQnM",
        "alias":"myBlockchainAlias"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/admin
```

#### **Example Response**

```text
{
    "jsonrpc":"2.0",
    "id"     :1,
    "result" :{
        "success":true
    }
}
```

Now, instead of interacting with the blockchain whose ID is `sV6o671RtkGBcno1FiaDbVcFv2sG5aVXMZYzKdP4VQAWmJQnM` by making API calls to `/ext/bc/sV6o671RtkGBcno1FiaDbVcFv2sG5aVXMZYzKdP4VQAWmJQnM`, one can also make calls to `ext/bc/myBlockchainAlias`.

### admin.lockProfile

Writes a profile of mutex statistics to `lock.profile`.

#### **Signature**

```text
admin.lockProfile() -> {success:bool}
```

#### **Example Call**

```text
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"admin.lockProfile",
    "params" :{}
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/admin
```

#### **Example Response**

```text
{
    "jsonrpc":"2.0",
    "id"     :1,
    "result" :{
        "success":true
    }
}
```

### admin.memoryProfile

Writes a memory profile of the to `mem.profile`.

#### **Signature**

```text
admin.memoryProfile() -> {success:bool}
```

#### **Example Call**

```text
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"admin.memoryProfile",
    "params" :{}
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/admin
```

#### **Example Response**

```text
{
    "jsonrpc":"2.0",
    "id"     :1,
    "result" :{
        "success":true
    }
}
```

### admin.startCPUProfiler

Start profiling the CPU utilization of the node. To stop, call `admin.stopCPUProfiler`. On stop, writes the profile to `cpu.profile`.

#### **Signature**

```text
admin.startCPUProfiler() -> {success:bool}
```

#### **Example Call**

```text
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"admin.startCPUProfiler",
    "params" :{}
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/admin
```

#### **Example Response**

```text
{
    "jsonrpc":"2.0",
    "id"     :1,
    "result" :{
        "success":true
    }
}
```

### admin.stopCPUProfiler

Stop the CPU profile that was previously started.

#### **Signature**

```text
admin.stopCPUProfiler() -> {success:bool}
```

#### **Example Call**

``` text 
curl -X POST --data'{ 
    “ jsonrpc”：“ 2.0”，
    “ id”：1，
    “ method”：“ admin.stopCPUProfiler” 
}'- H'content -type：application / json;' 127.0.0.1:9650/ext/admin ```


#### **示例响应** 

```文本
{ 
    “jsonrpc”： “2.0”，
    “ID”：1， 
    “结果”：{ 
        “成功”：真
    } 
} ```


<!--stackedit_data:
eyJoaXN0b3J5IjpbODk2NTUzNzQxXX0=
-->