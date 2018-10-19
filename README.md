# gRPC and Load Balancer(LB)

This is a description of how gRPC load balancing works with external load balancer.<br>
Before reading this paper, make sure you understand a concept of [Load Balancing in gRPC](https://github.com/grpc/grpc/blob/master/doc/load-balancing.md). <br>

This paper is more engaged in `grpclb policy`, `load_balancer` and `external_load_balacer` with little python gRPC client examples. Here are quick definitions.

| name                         | description |
| ---------------------------- | ----------- |
| **grpclb-policy(lb-policy)** | load balancing policy within gRPC communication. |
| **load_balancer**            | instance of load balancer corresponding lb-policy. |
| **external_load_balacer**    | external lb, not in gRPC. |

All the debug message on this paper, actually can be seen by setting gRPC debug ENV variables.  
```bash
export GRPC_TRACE=client_channel,pick_first,round_robin
export GRPC_VERBOSITY=DEBUG
```
**Environment**
- Ubuntu 16.04 LTS
- Python 3.6.5
- gRPC 1.15.0


**Table of Contents**
- [gRPC load balancing policies](#grpc-load-balancing-policies)
  - [pick_first](#pick_first)
  - [round_robin](#round_robin)
  - [Setting LB policy](#setting-lb-policy)
- [References](#references)

## gRPC Core

gRPC channel has connectivity state inside of it. And this is chaged by time. <br>
`IDLE -> CONNECTING -> READY -> SHUTDOWN`

There are two kind of LB policies in gRPC Core. `pick_first` and `round_robin`.


### pick_first
**Default** lb-policy. Picks a **first** element from subchannel list. Also, establishing only **one connection** with selected server.

### round_robin
Establishing **all connection** in subchannel list. Sending request one by one via round robin mechanism. 

### Setting LB policy 
```python
import grpc
channel = grpc.insecure_channel(
    target='localhost:9999', 
    options=[('grpc.lb_policy_name', 'round_robin'), ... ])
channel.close()
```
> You shuold call `channel.close()` explicitly. Or use with statement. ([channel.close](https://github.com/grpc/grpc/pull/15725))<br>
> `round_robin` policy work since [v1.14.0](https://github.com/grpc/grpc/releases/tag/v1.14.0).

Channel instance get grpc options. Python passes these args to the Core transparently.<br>
> See more core options: [gRPC arg keys](https://grpc.io/grpc/core/group__grpc__arg__keys.html)





## References
- https://github.com/grpc/grpc/blob/master/doc/load-balancing.md




