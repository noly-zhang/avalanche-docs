＃验证API

运行节点时，可以要求API调用附加授权令牌。此API管理授权令牌的创建和吊销。

授权令牌提供对一个或多个API端点的访问。这对于委派对节点API的访问很有用。令牌会在12小时后过期。

API调用的标头中提供了一个授权令牌。具体地讲，首部`授权`应具有值`承载TOKEN.GOES.HERE ` \（其中` TOKEN.GOES.HERE `被替换令牌\）。

如果该节点是开始使用此API仅可到达[命令行参数]（../引用/ command-line-interface.md）` --api-AUTH-需要`。如果在没有此CLI的情况下启动节点，则API调用不需要授权令牌，因此无法访问此API。此API永远不需要访问授权令牌。

必须创建授权令牌。如果你运行你的节点` --api认证-需要'，你还必须指定参数授权令牌的密码` --api-认证密码`。您必须提供此密码才能创建/撤消授权令牌。

请注意，如果你运行你的节点` --api认证-需要`然后一些像MetaMask工具可能无法进行API调用到您的节点，因为他们没有一个身份验证令牌。

##格式

该API使用` JSON 2.0 ` RPC格式。有关进行JSON RPC调用的更多信息，请参见[这里。]（issuing-api-calls.md）

##端点

```文
/转/ AUTH ```


##方法

### auth.newToken

创建一个新的授权令牌，该令牌授予对一个或多个API端点的访问权限。

#### **签名** 

```cpp
auth.newToken(
    {
        password: string,
        endpoints: []string
    }
) -> {token: string}
```

* `password` is this node’s authorization token password.
* `endpoints` is a list of endpoints that will be accessible using the generated token. If `endpoints` contains an element `"*"`, the generated token can access any API endpoint.
* `token` is the authorization token.

#### **Example Call**

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "auth.newToken",
    "params":{
        "password":"YOUR PASSWORD GOES HERE",
        "endpoints":["/ext/bc/X", "/ext/info"]
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/auth
```

This call will generate an authorization token that allows access to API endpoints `/ext/bc/X` \(ie the X-Chain\) and `/ext/info` \(ie the [info API](info-api.md).\)

#### **Example Response**

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJFbmRwb2ludHMiOlsiKiJdLCJleHAiOjE1OTM0NzU4OTR9.Cqo7TraN_CFN13q3ae4GRJCMgd8ZOlQwBzyC29M6Aps"
    },
    "id": 1
}
```

This authorization token should be included in API calls by giving header `Authorization` value `Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJFbmRwb2ludHMiOlsiKiJdLCJleHAiOjE1OTM0NzU4OTR9.Cqo7TraN_CFN13q3ae4GRJCMgd8ZOlQwBzyC29M6Aps`.

For example, to call [`info.peers`](info-api.md#info-peers) with this token:

```cpp
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"info.peers"
}' 127.0.0.1:9650/ext/info \
-H 'content-type:application/json;' \
-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJFbmRwb2ludHMiOlsiKiJdLCJleHAiOjE1OTM0NzU4OTR9.Cqo7TraN_CFN13q3ae4GRJCMgd8ZOlQwBzyC29M6Aps'
```

### auth.revokeToken

Revoke a previously generated token. The given token will no longer grant access to any endpoint. If the token is invalid, does nothing.

#### **Signature**

```cpp
auth.revokeToken(
    {
        password: string,
        token: string
    }
) -> {success: bool}
```

* `password` is this node’s authorization token password.
* `token` is the authorization token being revoked.

#### **Example Call**

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "auth.revokeToken",
    "params":{
        "password":"123",
        "token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1OTMxNzIzMjh9.qZVNhH6AMQ_LpbXnPbTFEL6Vm5EM5FLU-VEKpYBH3k4"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/auth
```

#### **Example Response**

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "success": true
    },
    "id": 1
}
```

### auth.changePassword

Change this node’s authorization token password. Any authorization tokens created under an old password will become invalid.

#### **Signature**

```cpp
auth.changePassword(
    {
        oldPassword: string,
        newPassword: string
    }
) -> {success: bool}
```

* `oldPassword` is this node’s current authorization token password.
* `newPassword` is the node’s new authorization token password after this API call. Must be between 1 and 1024 characters.

#### **Example Call**

```cpp
curl -X POST --data '{
    "jsonrpc": "2.0",
    "method": "auth.changePassword",
    "params":{
        "oldPassword":"OLD PASSWORD HERE",
        "newPassword":"NEW PASSWORD HERE"
    },
    "id": 1
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/验证```


#### **示例响应** 

```cpp { “ jsonrpc” ：“ 2.0” ，“ result” ：{ “成功” ：true } ，“ id” ：1 } `''

     
     
         
    
     



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NjQwMDU4NTddfQ==
-->