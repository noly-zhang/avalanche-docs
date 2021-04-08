＃健康API

该API可用于测量节点运行状况。

为了得到一个HTTP状态代码响应，指示节点的健康，做一个' GET '请求' / EXT /健康'。如果节点运行状况良好，它将返回状态代码“ 200 ”。如果需要有关节点运行状况的更深入的信息，请使用以下方法。

##格式

该API使用` JSON 2.0 ` RPC格式。有关进行JSON RPC调用的更多信息，请参见[ here ]（issuing-api-calls.md）。

##端点

```文本
/ ext / health```


##方法

### health.getLiveness

该节点每30秒运行一组运行状况检查，包括每个链的运行状况检查。此方法返回最后一组运行状况检查结果。

#### **签名** 

```cpp
health.getLiveness() -> {
    checks: []{
        checkName: {
            message: JSON,
            error: JSON,
            timestamp: string,
            duration: int,
            contiguousFailures: int,
            timeOfFirstFailure: int
        }
    },
    healthy: bool
}
```

`healthy` is true if the node if all health checks are passing.

`checks` is a list of health check responses.

* A check response may include a `message` with additional context.
* A check response may include an `error` describing why the check failed.
* `timestamp` is the timestamp of the last health check.
* `duration` is the execution duration of the last health check, in nanoseconds.
* `contiguousFailures` is the number of times in a row this check failed.
* `timeOfFirstFailure` is the time this check first failed.

More information on these measurements can be found in the documentation for the [go-sundheit](https://github.com/AppsFlyer/go-sundheit) library.

#### **Example Call**

```cpp
curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"health.getLiveness"
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/health
```

#### **Example Response**

In this example response, the C-Chain’s health check is failing.

```cpp
{
    "jsonrpc": "2.0",
    "result": {
        "checks": {
            "C": {
                "message": null,
                "error": {
                    "message": "example error message"
                },
                "timestamp": "2020-10-14T14:04:20.57759662Z",
                "duration": 465253,
                "contiguousFailures": 50,
                "timeOfFirstFailure": "2020-10-14T13:16:10.576435413Z"
            },
            "P": {
                "message": {
                    "percentConnected": 0.9967694992864075
                },
                "timestamp": "2020-10-14T14:04:08.668743851Z",
                "duration": 433363830,
                "contiguousFailures": 0,
                "timeOfFirstFailure": null
            },
            "X": {
                "timestamp": "2020-10-14T14:04:20.3962705Z",
                "duration": 1853,
                "contiguousFailures": 0,
                "timeOfFirstFailure": null
            },
            "chains.default.bootstrapped": {
                "timestamp": "2020-10-14T14:04:04.238623814Z",
                "duration": 8075,
                "contiguousFailures": 0,
                "timeOfFirstFailure": null
            },
            "network.validators.heartbeat": {
                "message": {
                    "heartbeat": 1602684245
                },
                "timestamp": "2020-10-14T14:04:05.610007874Z",
                "duration": 6124,
                "contiguousFailures": 0 ，“ timeOfFirstFailure” ： null } } ，“ healthy” ：false } ，“ id” ：1 } ```
                
            
        
         
    
     



<!--stackedit_data:
eyJoaXN0b3J5IjpbOTU3MDM5ODMwXX0=
-->