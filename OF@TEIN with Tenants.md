# OF@TEIN+ with Tenants

![img](C:\Users\khooi\Documents\GitHub\documentations\images\of@tein tenants.JPG)

> Tenant A:
>
> + A1 (10.0.0.1/8)
> + A2 (10.0.0.2/8)
> + A3 (10.0.0.3/8)
>
> Tenant B
>
> + B1 (10.0.0.1/8)
> + B2 (10.0.0.2/8)
>
> Tenant C 
>
> + C1 (192.168.1.1/24)
> + C2 (192.168.1.2/24)

## Port Connections

#### SW_MY_1 (of:1122334455667701)

+ `A1-eth0` - `SW_MY_1-eth1`
+ `B1-eth0` - `SW_MY_1-eth2`
+ `C1-eth0` - `SW_MY_1-eth3`
+ `SW_KR_1-eth3` - `SW_MY_1-eth4`
+ `SW_TW_1-eth4` - `SW_MY_1-eth5`

#### SW_KR_1 (of:1122334455667702)

- `A2-eth0` - `SW_KR_1-eth1`
- `B2-eth0` - `SW_KR_1-eth2`
- `SW_MY_1-eth4` - `SW_KR_1-eth3`
- `SW_TW_1-eth3` - `SW_KR_1-eth4`

#### SW_TW_1 (of:1122334455667703)

- `A3-eth0` - `SW_TW_1-eth1`
- `C2-eth0` - `SW_TW_1-eth2`
- `SW_JR_1-eth3` - `SW_KR_1-eth3`
- `SW_MY_1-eth5` - `SW_TW_1-eth4`


## Configurations

### Clear Flows

```bash
sudo ovs-ofctl del-flows SW_MY_1 -O OpenFlow13
sudo ovs-ofctl del-flows SW_KR_1 -O OpenFlow13
sudo ovs-ofctl del-flows SW_TW_1 -O OpenFlow13
```

### SW_MY_1

```
# Switch-Switch Links (Table 2)
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=0,in_port=4,actions=goto_table:2"
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=0,in_port=5,actions=goto_table:2"

# Tenant A
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=0,in_port=1,actions=goto_table:1"

# A1 -> A2
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.2,actions=push_mpls:0x8847,set_field:4211->mpls_label,output:4"

# A1 -> A3
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.3,actions=push_mpls:0x8847,set_field:2312->mpls_label,output:5"

# * -> A1
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=2,eth_type=0x8847,mpls_bos=1,mpls_label=232,actions=pop_mpls:0x800,output:1"

# Tenant B
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=0,in_port=2,actions=goto_table:1"

# B1 -> B2
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=1,in_port=2,eth_type=0x800,nw_dst=10.0.0.2,actions=push_mpls:0x8847,set_field:3532->mpls_label,output:4"

# * -> B1
sudo ovs-ofctl -O OpenFlow13 add-flow SW_MY_1 "table=2,eth_type=0x8847,mpls_bos=1,mpls_label=333,actions=pop_mpls:0x800,output:2"
```

### SW_KR_1

```
# Switch-Switch Links (Table 2)
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=0,in_port=3,actions=goto_table:2"
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=0,in_port=4,actions=goto_table:2"

# Tenant A
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=0,in_port=1,actions=goto_table:1" 

# A2 -> A1
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.1,actions=push_mpls:0x8847,set_field:232->mpls_label,output:3"

# A2 -> A3
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.3,actions=push_mpls:0x8847,set_field:2312->mpls_label,output:4"

# * -> A2
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=2,eth_type=0x8847,mpls_bos=1,mpls_label=4211,actions=pop_mpls:0x800,output:1"

# Tenant B
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=0,in_port=2,actions=goto_table:1" 

# B2 -> B1
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=1,in_port=2,eth_type=0x800,nw_dst=10.0.0.1,actions=push_mpls:0x8847,set_field:333->mpls_label,output:3"

# * -> B2
sudo ovs-ofctl -O OpenFlow13 add-flow SW_KR_1 "table=2,eth_type=0x8847,mpls_bos=1,mpls_label=3532,actions=pop_mpls:0x800,output:2"
```

### SW_TW_1

```
# Switch-Switch Links (Table 2)
sudo ovs-ofctl -O OpenFlow13 add-flow SW_TW_1 "table=0,in_port=3,actions=goto_table:2"
sudo ovs-ofctl -O OpenFlow13 add-flow SW_TW_1 "table=0,in_port=4,actions=goto_table:2"

# Tenant A
sudo ovs-ofctl -O OpenFlow13 add-flow SW_TW_1 "table=0,in_port=1,actions=goto_table:1" 

# A3 -> A1
sudo ovs-ofctl -O OpenFlow13 add-flow SW_TW_1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.1,actions=push_mpls:0x8847,set_field:232->mpls_label,output:4"

# A3 -> A2
sudo ovs-ofctl -O OpenFlow13 add-flow SW_TW_1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.2,actions=push_mpls:0x8847,set_field:4211->mpls_label,output:3"

# * -> A3
sudo ovs-ofctl -O OpenFlow13 add-flow SW_TW_1 "table=2,eth_type=0x8847,mpls_bos=1,mpls_label=2312,actions=pop_mpls:0x800,output:1"
```

### Static ARP Configuration

```
# A1
arp -s 10.0.0.2 0A:00:00:00:00:02
arp -s 10.0.0.3 0A:00:00:00:00:03

# A2 
arp -s 10.0.0.1 0A:00:00:00:00:01
arp -s 10.0.0.3 0A:00:00:00:00:03

# A3
arp -s 10.0.0.1 0A:00:00:00:00:01
arp -s 10.0.0.2 0A:00:00:00:00:02

# B1
arp -s 10.0.0.2 0B:00:00:00:00:02

# B2
arp -s 10.0.0.1 0B:00:00:00:00:01
```





## MPLS Forwarding Tables

#### SW_MY_1

| Local Label     | Outgoing Label | Host             | Next Hop |
| --------------- | -------------- | ---------------- | -------- |
| 232             | pop            | A1 (10.0.0.1)    | 1        |
| 2321 / untagged | 4211           | A2 (10.0.0.2)    | 4        |
| 2323 / untagged | 2312           | A3 (10.0.0.3)    | 5        |
| 333             | pop            | B1 (10.0.0.1)    | 2        |
| 312 / untagged  | 3532           | B2 (10.0.0.2)    | 4        |
| 2421            | pop            | C1 (192.168.1.1) | 3        |
| 3274 / untagged | 3232           | C2 (192.168.1.2) | 5        |

#### SW_KR_1

| Local Label     | Outgoing Label | Host             | Next Hop |
| --------------- | -------------- | ---------------- | -------- |
| 3123 / untagged | 232            | A1 (10.0.0.1)    | 3        |
| 4211            | pop            | A2 (10.0.0.2)    | 1        |
| 234 / untagged  | 2312           | A3 (10.0.0.3)    | 4        |
| 321 / untagged  | 333            | B1 (10.0.0.1)    | 3        |
| 3532            | pop            | B2 (10.0.0.2)    | 2        |
| 2313 / untagged | ??             | C1 (192.168.1.1) | ??       |
| 2355 / untagged | ??             | C2 (192.168.1.2) | ??       |

#### SW_TW_1

| Local Label      | Outgoing Label | Host             | Next Hop |
| ---------------- | -------------- | ---------------- | -------- |
| 123 / untagged   | 232            | A1 (10.0.0.1)    | 4        |
| 3221 / untagged  | 4211           | A2 (10.0.0.2)    | 3        |
| 2312             | pop            | A3 (10.0.0.3)    | 1        |
| 2321 / untagged  | ??             | B1 (10.0.0.1)    | ??       |
| 21312 / untagged | ??             | B2 (10.0.0.2)    | ??       |
| 3233 / untagged  | 2421           | C1 (192.168.1.1) | 4        |
| 3232             | pop            | C2 (192.168.1.2) | 2        |

> Note: Currently only adjacent paths are used, no LSRs required. Hence ?? entries exist