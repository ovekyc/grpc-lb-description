# gRPC Load Balancing

This is a description of how gRPC load balancing works with load balancer.<br>
This paper is written with python client example.<br>
All the debug message on this paper, actually can be seen by setting gRPC debug env variables.
   
```bash
export GRPC_TRACE=client_channel,pick_first,round_robin,glb
export GRPC_VERBOSITY=DEBUG
```

#### Environment
- Ubuntu 16.04 LTS
- Python 3.6.5
- gRPC 1.15.0


## gRPC load balancing policies

There are two kind of gRPC LB policies. `first_pick` and `round_robin`.
This can be specified on options which is passed to channel parameter.

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
Which means grpc options, including lb-policy, are valid within a channel instance.
> See more core options: [gRPC arg keys](https://grpc.io/grpc/core/group__grpc__arg__keys.html)

#### first_pick

This is a default lb-policy. <br>
As you can see on the name of it, this picks a first element from lb-list. Also, this establish only one connection with selected server.






# References
- https://github.com/grpc/grpc/blob/master/doc/load-balancing.md




