# gRPC Load Balancing

This is a description of how gRPC load balancing works with load balancer.<br>
This paper is written with python client example.

#### Environment
- Ubuntu 16.04 LTS
- Python 3.6.5
- gRPC 1.15.0


## gRPC load balancing policies

There are two kind of gRPC LB policies. `first_pick` and `round_robin`.
This can be specified on options which is passed to channel parameter.

```python
import grpc
channel = grpc.insecure_channel(target='localhost:9999', 
                                options=[('grpc.lb_policy_name', 'round_robin'), ... ]
....
channel.close()
```
> You shuold call `channel.close()` explicitly. Or use with statement. ([channel.close()](https://github.com/grpc/grpc/pull/15725))<br>
> `round_robin` policy work since [v1.14.0](https://github.com/grpc/grpc/releases/tag/v1.14.0).


You might notice that channel instance get some grpc options. <br>
Python passes these args to the Core transparently.
> See more core options: [gRPC arg keys](https://grpc.io/grpc/core/group__grpc__arg__keys.html)




# References
- https://github.com/grpc/grpc/blob/master/doc/load-balancing.md




