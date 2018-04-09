# VXLAN Configuration

```
ovs-vsctl add-port br-int vxlan0 -- set interface vxlan0 type=vxlan options:remote_ip=172.168.1.2
```

Retrieve port Id

```
ovs-vsctl get Interface eth0 ofport
```

