<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [gRPC Load Balancing](#grpc-load-balancing)
      - [Environment](#environment)
  - [gRPC load balancing policies](#grpc-load-balancing-policies)
    - [pick_first](#pick_first)
    - [round_robin](#round_robin)
    - [Setting LB policy](#setting-lb-policy)
- [References](#references)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

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
There are two kind of gRPC LB policies. `pick_first` and `round_robin`.

### pick_first
Title is description. **Default** lb-policy.<br>
Picks a **first** element from subchannel list. Also, establishing only **one connection** with selected server.
```
subchannel_list.h:323]        [pick_first 0x7faaeb1686b0] subchannel list 0x7faaed85e340 index 0 of 4 (subchannel 0x7faaed8d6000): starting watch: requesting connectivity change notification (from IDLE)
subchannel_list.h:435]        [pick_first 0x7faaeb1686b0] subchannel list 0x7faaed85e340 index 0 of 4 (subchannel 0x7faaed8d6000): connectivity changed: state=CONNECTING, error="No Error", shutting_down=0
subchannel_list.h:344]        [pick_first 0x7faaeb1686b0] subchannel list 0x7faaed85e340 index 0 of 4 (subchannel 0x7faaed8d6000): renewing watch: requesting connectivity change notification (from CONNECTING)
client_channel.cc:189]        chand=0x7faaedbc1228: lb_policy=0x7faaeb1686b0 state changed to CONNECTING
```
### round_robin
Title is description. You can specify this lb-policy through channel options.<br>
Establishing **all connection** in subchannel list.
```
subchannel_list.h:323]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 0 of 4 (subchannel 0x7fad7586f3c0): starting watch: requesting connectivity change notification (from IDLE)
subchannel_list.h:323]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 1 of 4 (subchannel 0x7fad75865f40): starting watch: requesting connectivity change notification (from IDLE)
subchannel_list.h:323]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 2 of 4 (subchannel 0x7fad758717c0): starting watch: requesting connectivity change notification (from IDLE)
subchannel_list.h:323]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 3 of 4 (subchannel 0x7fad75875fc0): starting watch: requesting connectivity change notification (from IDLE)
subchannel_list.h:435]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 0 of 4 (subchannel 0x7fad7586f3c0): connectivity changed: state=CONNECTING, error="No Error", shutting_down=0
round_robin.cc:560]           [RR 0x7fad82eef440] connectivity changed for subchannel 0x7fad7586f3c0, subchannel_list 0x7fad843a5810 (index 0 of 4): prev_state=IDLE new_state=CONNECTING
subchannel_list.h:344]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 0 of 4 (subchannel 0x7fad7586f3c0): renewing watch: requesting connectivity change notification (from CONNECTING)
subchannel_list.h:435]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 1 of 4 (subchannel 0x7fad75865f40): connectivity changed: state=CONNECTING, error="No Error", shutting_down=0
round_robin.cc:560]           [RR 0x7fad82eef440] connectivity changed for subchannel 0x7fad75865f40, subchannel_list 0x7fad843a5810 (index 1 of 4): prev_state=IDLE new_state=CONNECTING
subchannel_list.h:344]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 1 of 4 (subchannel 0x7fad75865f40): renewing watch: requesting connectivity change notification (from CONNECTING)
subchannel_list.h:435]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 2 of 4 (subchannel 0x7fad758717c0): connectivity changed: state=CONNECTING, error="No Error", shutting_down=0
round_robin.cc:560]           [RR 0x7fad82eef440] connectivity changed for subchannel 0x7fad758717c0, subchannel_list 0x7fad843a5810 (index 2 of 4): prev_state=IDLE new_state=CONNECTING
subchannel_list.h:344]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 2 of 4 (subchannel 0x7fad758717c0): renewing watch: requesting connectivity change notification (from CONNECTING)
subchannel_list.h:435]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 3 of 4 (subchannel 0x7fad75875fc0): connectivity changed: state=CONNECTING, error="No Error", shutting_down=0
round_robin.cc:560]           [RR 0x7fad82eef440] connectivity changed for subchannel 0x7fad75875fc0, subchannel_list 0x7fad843a5810 (index 3 of 4): prev_state=IDLE new_state=CONNECTING
subchannel_list.h:344]        [round_robin 0x7fad82eef440] subchannel list 0x7fad843a5810 index 3 of 4 (subchannel 0x7fad75875fc0): renewing watch: requesting connectivity change notification (from CONNECTING)
client_channel.cc:189]        chand=0x7fad82fee5f8: lb_policy=0x7fad82eef440 state changed to CONNECTING
```

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





# References
- https://github.com/grpc/grpc/blob/master/doc/load-balancing.md




