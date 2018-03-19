# OVS Configuration for Single Machine OpenNebula

Operating system version used in this guide:

> Ubuntu 16.04.3 LTS
>
> Open vSwitch 2.9.90

A flat network architecture (no VLANs) is used whereby the address **192.168.0.50 - 192.168.0.79** are reserved for OpenNebula virtual machine instances. You may refer to the network architecture diagram below:

![OpenNebula Network Architecture](/images/opennebula_network_architecture.png)

## Step One: Installing OVS

1. You can choose to compile and install OVS from source, however, in this example, we will be installing Open vSwitch through the package manager. Super user privilege is required.

   ```bash
   sudo apt install openvswitch-switch
   ```

## Step Two: Configuring Promiscuous Mode for your physical network adapter

1. Quoted from Open vSwitch's official documentation regarding Promiscuous Mode:

   ```
   Conventionally, “promiscuous mode” is a feature of a network interface card. Ordinarily, a NIC passes to the CPU only the packets actually destined to its host machine. It discards the rest to avoid wasting memory and CPU cycles. 

   When promiscuous mode is enabled, however, it passes every packet to the CPU. On an old-style shared-media or hub-based network, this allows the host to spy on all packets on the network. But in the switched networks that are almost everywhere these days, promiscuous mode doesn’t have much effect, because few packets not destined to a host are delivered to the host’s NIC.

   This form of promiscuous mode is configured in the guest OS of the VMs on your bridge, e.g. with “ip link set <device> promisc”.
   ```

2. Hence in order to allow our NIC to forward frames that are destined to our virtual machines, we will have set our physical network adapter, in our case, `eno1` to have Promiscuous Mode turned on. The network adapter varies from one to another depending on your machine, it could be `eth0`, `enp0s3` or others.

   ```bash
   ip link set eno1 promisc on
   ```

## Step Three: Creating the OVS bridge

1. Now it's the time for us to bring up our own OVS bridge for the virtual network within our machine. In this case, we will call our bridge `br0`.

   ```bash
   sudo ovs-vscrl add-br br0
   ```

## Step Four: Assigning network interfaces

1. Subsequently after creating our OVS bridge, we will need to attach/ assign the relevant network interfaces, in this case, which is `eno1`.

   ```bash
   sudo ovs-vsctl add-port br0 eno1
   ```

2. Upon adding our only physical network adapter `eno1` to the OVS bridge, you will lose all internet connectivity. Do not panic, quickly follow the next step!

## Step Five: Modifying the network configuration

1. Quoted again, from Open vSwitch's official documentation about losing internet connectivity upon adding an interface to an OVS bridge.

   ```
    A physical Ethernet device that is part of an Open vSwitch bridge should not have an IP address. If one does, then that IP address will not be fully functional.

   You can restore functionality by moving the IP address to an Open vSwitch “internal” device, such as the network device named after the bridge itself.
   ```

2. The `internal` interface of an OVS bridge is highly similar with the SVI of a physical network switch. Hence, in order to fix this situation, we will have to clear out the IP of the `eno1` interface and reassign it to the `internal` interface.

   ```bash
   sudo ip addr flush dev eno1
   ```

3. In our example, we will be using 192.168.0.99/24 as the static IP for our physical machine. We will be stating explicitly the network configurations in the `/etc/network/interfaces` file. Below will be the example entries in our `/etc/network/interfaces` file.

   > Note: You will need to be a super user in order to edit the file. 
   >
   > Hint: `sudo nano /etc/network/interfaces`

   ```
   auto lo
   iface lo inet loopback

   auto eno1	# you must add this so that the interface will be brought up automatically
   iface lo inet static

   auto br0	# bring up br0 automatically
   iface br0 inet static
   	address 192.168.0.99
   	netmask 255.255.255.0
   	gateway 192.168.0.1 
   	network 192.168.0.0
   	broadcast 192.168.0.255
   	dns-nameservers 8.8.8.8
   ```

4. Upon saving the network configuration file, restart all the Ubuntu networking service.

   ```bash
   sudo systemctl restart networking
   sudo systemctl restart network-manager
   ```

   However, if in doubt, you can disable and enable the network interfaces one by one manually.

   ```bash
   sudo ip link set eno1 down
   sudo ip link set br0 down
   sudo ip link set eno1 up
   sudo ip link set br0 up
   ```

   By now you should have regained internet connectivity. If issues persists, try rebooting your machine or double check your `/etc/network/interfaces` for typos.

## Step Six: Setting up your virtual network in OpenNebula

TBC

