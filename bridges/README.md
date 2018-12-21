**What is a bridge?** 
- A device that separates 2/more network segments within one **logical** network (ip-subnet)
- acts as a switch

**More:**
- **job:** examine destination of the data packets and decides whether to pass the packet to the other side of the segment
- **why?:** faster,quieter network with less collisions
- placed between two groups of computer that don't talk that much with computers of other group
- looks at IP,IPX,NetBEUI but not at the MAC-address to each NIC
- **what is?** not a router nor a firewall, but behaves like a **switch (Layer 2)**

**Features above pure bridging?**
- STP (Spanning Tree Protocol)
- Multiple Bridge Instances
- Fire-walling

#### Rules on bridging
- a port (NIC) can only be a member of one bridge
- knows nothing about routes
- knows nothing about higher protocols than ARP
- it's covered by only one logical interface
- When NIC added to a bridge -> you lose direct control

**Notes:**
- ssh(best)/SNMP/telnet/rlogin for remote management
- for remote management needs an IP (exception to transparency)


#### Set up

Add kernel module
```
modprobe -v bridge
# check if present
cat /proc/modules | grep bridge
```

Create instance
```
sudo brctl addbr mybridge1
sudo brctl show
#STP a network protocol that builds a loop-free logical topology for Ethernet networks
sudo brctl showstp mybridge1
```
#### Bring up the bridge
```
sudo ip link set dev mybridge1 up
```

#### Enslave network device
```
sudo brctl addif mybridge1 <eth0>
sudo brctl showstp mybridge1 #now <eth0> is added with path cost but disabled
brctl showmacs mybridge1 #now we have 2 macs (identical) 
```
**Note:** if you enslave an interface on a bridge you will not able to ping the interface anymore
**To know more:** [here](https://www.tldp.org/HOWTO/BRIDGE-STP-HOWTO/set-up-the-bridge.html)

Delete instance
```
sudo brctl delif mybridge1 <eth0>
sudo brctl delbr mybridge1
```

### Note: if we enslave the interface directly we lose connectivity --> tun/tap


### Address Resolution Protocol (ARP)
- used by IPv4
- maps IP to hardware address used by data link protocol

**goal?** finding an address of a computer in a network.

**Protocol Example:**
**Scenario**
- Computer 1 has a packet to send to Computer 2. 
- Through DNS, it determines that Computer 2 has the IP address 192.168.0.55.
- Computer 2's MAC address. First, Computer 1 uses a cached ARP table to look up 192.168.0.55 for any existing records of Computer 2's MAC address (00:eb:24:b2:05:ac). 
- If the MAC address is found, 
 - it sends an Ethernet frame with destination address 00:eb:24:b2:05:ac, containing the IP packet onto the link. 
- If the cache did not produce a result for 192.168.0.55, 
 - Computer 1 has to send a broadcast ARP message (destination FF:FF:FF:FF:FF:FF MAC address) requesting an answer for 192.168.0.55.(accepted by all computer on the lan) 
- Computer 2 responds with its MAC and IP addresses.

Computer 2 may insert an entry for Computer 1 into its ARP table for future use.

Computer 1 caches the response information in its ARP table and can now send the packet.



