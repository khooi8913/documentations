### OpenFlow13 Setup
```bash
sudo ovs-vsctl set bridge s1 protocols=OpenFlow13
sudo ovs-vsctl set bridge s2 protocols=OpenFlow13
sudo ovs-vsctl set bridge s3 protocols=OpenFlow13
```

### Delete Flows
```bash
sudo ovs-ofctl del-flows s1
sudo ovs-ofctl del-flows s2
sudo ovs-ofctl del-flows s3
```

### S1
```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=1,eth_type=0x800,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=2,eth_type=0x8847,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.2,actions=push_mpls:0x8847,set_field:12->mpls_label,output:2"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.3,actions=push_mpls:0x8847,set_field:13->mpls_label,output:2"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=2,eth_type=0x8847,mpls_bos=1,mpls_label=21,actions=pop_mpls:0x800,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=1,in_port=2,eth_type=0x8847,mpls_bos=1,mpls_label=31,actions=pop_mpls:0x800,output:1"
```

### S2
```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=0,in_port=1,eth_type=0x800,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=0,in_port=2,eth_type=0x8847,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=0,in_port=3,eth_type=0x8847,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.1,actions=push_mpls:0x8847,set_field:21->mpls_label,output:2"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.3,actions=push_mpls:0x8847,set_field:23->mpls_label,output:3"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=1,in_port=2,eth_type=0x8847,mpls_bos=1,mpls_label=12,actions=pop_mpls:0x800,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=1,in_port=3,eth_type=0x8847,mpls_bos=1,mpls_label=32,actions=pop_mpls:0x800,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=1,in_port=2,eth_type=0x8847,mpls_bos=1,mpls_label=13,actions=output:3"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=1,in_port=3,eth_type=0x8847,mpls_bos=1,mpls_label=31,actions=output:2"
```

### S3
```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=0,in_port=1,eth_type=0x800,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=0,in_port=2,eth_type=0x8847,actions=goto_table:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.1,actions=push_mpls:0x8847,set_field:31->mpls_label,output:2"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=1,in_port=1,eth_type=0x800,nw_dst=10.0.0.2,actions=push_mpls:0x8847,set_field:32->mpls_label,output:2"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=1,in_port=2,eth_type=0x8847,mpls_bos=1,mpls_label=13,actions=pop_mpls:0x800,output:1"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=1,in_port=2,eth_type=0x8847,mpls_bos=1,mpls_label=23,actions=pop_mpls:0x800,output:1"
```

### ARP
```bash
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=1,eth_type=0x806,actions=FLOOD"
sudo ovs-ofctl -O OpenFlow13 add-flow s1 "table=0,in_port=2,eth_type=0x806,actions=FLOOD"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=0,in_port=1,eth_type=0x806,actions=FLOOD"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=0,in_port=2,eth_type=0x806,actions=FLOOD"
sudo ovs-ofctl -O OpenFlow13 add-flow s2 "table=0,in_port=3,eth_type=0x806,actions=FLOOD"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=0,in_port=1,eth_type=0x806,actions=FLOOD"
sudo ovs-ofctl -O OpenFlow13 add-flow s3 "table=0,in_port=2,eth_type=0x806,actions=FLOOD"
```
