### Two Mininet Connected via VXLAN

> Site A:
>
> S1-Eth1 - 10.0.0.1 (Tenant A) -- PortId= 1
>
> S1-Eth2 - 10.0.0.2 (Tenant B) -- PortId= 2
>
> VXLAN -- PortId = 3
>
> Site B:
>
> S2-Eth1 - 10.0.0.1(Tenant B) -- PortId= 1
>
> S2-Eth2 - 10.0.0.2(Tenant A) -- PortId= 2
>
> VXLAN -- PortId = 3

![img](/images/mplsof13.JPG)

```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=1,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=2,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=3,actions=goto_table:1"

# Site A 172.31.39.59
# Outgoing IP Packets (0x8847)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=1,eth_type=0x800,actions=push_mpls:0x8847,set_field:100->mpls_label,output:3"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=2,eth_type=0x800,actions=push_mpls:0x8847,set_field:200->mpls_label,output:3"

# Outgoing ARP Packets (0x8848)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=1,eth_type=0x806,actions=push_mpls:0x8848,set_field:100->mpls_label,output:3"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=2,eth_type=0x806,actions=push_mpls:0x8848,set_field:200->mpls_label,output:3"

# Incoming IP Packets (0x8847)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8847,mpls_bos=1,mpls_label=300,actions=pop_mpls:0x800,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8847,mpls_bos=1,mpls_label=400,actions=pop_mpls:0x800,output:2"
# Incoming ARP Packets (0x8848)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8848,mpls_bos=1,mpls_label=300,actions=pop_mpls:0x806,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8848,mpls_bos=1,mpls_label=400,actions=pop_mpls:0x806,output:2"

# Site B 172.31.42.150
# Outgoing IP Packets (0x8847)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=1,eth_type=0x800,actions=push_mpls:0x8847,set_field:400->mpls_label,output:3"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=2,eth_type=0x800,actions=push_mpls:0x8847,set_field:300->mpls_label,output:3"

# Outgoing ARP Packets (0x8848)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=1,eth_type=0x806,actions=push_mpls:0x8848,set_field:400->mpls_label,output:3"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=2,eth_type=0x806,actions=push_mpls:0x8848,set_field:300->mpls_label,output:3"

# Incoming IP Packets (0x8847)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8847,mpls_bos=1,mpls_label=200,actions=pop_mpls:0x800,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8847,mpls_bos=1,mpls_label=100,actions=pop_mpls:0x800,output:2"

# Incoming ARP Packets (0x8848)
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8848,mpls_bos=1,mpls_label=200,actions=pop_mpls:0x806,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=3,eth_type=0x8848,mpls_bos=1,mpls_label=100,actions=pop_mpls:0x806,output:2"

```

#### tcpdump on VXLAN Tunnel @ Site B

> Tenant A-10.0.0.1 (Site A) ping Tenant A-10.0.0.2 (Site B)

```
01:36:28.850257 MPLS (label 100, exp 0, [S], ttl 64)
        0x0000:  0001 0800 0604 0001 021e 0826 c2d4 0a00  ...........&....
        0x0010:  0001 0000 0000 0000 0a00 0002            ............
01:36:28.850271 MPLS (label 300, exp 0, [S], ttl 64)
        0x0000:  0001 0800 0604 0002 1e22 8691 1706 0a00  ........."......
        0x0010:  0002 021e 0826 c2d4 0a00 0001            .....&......
01:36:38.736419 MPLS (label 100, exp 0, [S], ttl 64) IP ip-10-0-0-1.us-west-2.compute.internal > ip-10-0-0-2.us-west-2.compute.internal: ICMP echo request, id 4381, seq 1, length 64
01:36:38.736468 MPLS (label 300, exp 0, [S], ttl 64) IP ip-10-0-0-2.us-west-2.compute.internal > ip-10-0-0-1.us-west-2.compute.internal: ICMP echo reply, id 4381, seq 1, length 64
01:36:38.738229 MPLS (label 200, exp 0, [S], ttl 64) IP ip-10-0-0-2.us-west-2.compute.internal > ip-10-0-0-1.us-west-2.compute.internal: ICMP echo request, id 4382, seq 1, length 64
01:36:38.738257 MPLS (label 400, exp 0, [S], ttl 64) IP ip-10-0-0-1.us-west-2.compute.internal > ip-10-0-0-2.us-west-2.compute.internal: ICMP echo reply, id 4382, seq 1, length 64
01:36:43.747259 MPLS (label 200, exp 0, [S], ttl 64)
        0x0000:  0001 0800 0604 0001 0abe db2c 366b 0a00  ...........,6k..
        0x0010:  0002 0000 0000 0000 0a00 0001            ............
01:36:43.747595 MPLS (label 400, exp 0, [S], ttl 64)
        0x0000:  0001 0800 0604 0002 e616 e7c0 7d1a 0a00  ............}...
        0x0010:  0001 0abe db2c 366b 0a00 0002            .....,6k....
```

