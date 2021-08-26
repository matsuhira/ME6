# What is ME6
This is a technology for Ethernet over IPv6 encapsulation using the ME6 address (ME6A) that maps the MAC address to the IPv6 address.<br>

ME6A includes a plane-ID to handle duplicate MAC addresses and tenants.<br>
In reality, if the MAC address is 48 bits and the IPv6 Prefix is ​​64 bits, 16 bits can be assigned to the Plane-ID, so 2 ^ 16 = about 60,000 tenant networks can be accommodated. If you want to accommodate more tenants, if you set the IPv6 prefix to 48bit, you can allocate 32bit to the Plane-ID, so 2 ^ 32 = about 4.2 billion tenant networks can be accommodated.<br>
The following is the ME6A address format. The specifications are for both EUI-48 and EUI-64.<br>

```
|    128 - m -n bits    |        m bits        |       n bits     |
+-----------------------+----------------------+------------------+
|      ME6A prefix      |  Ethernet plane ID   | Ethernet address |
+-----------------------+----------------------+------------------+
```
ME6 is an abbreviation for Multiple Ethernet --IPv6 address mapping, and ME6A is an abbreviation for Multiple Ethernet --IPv6 mapped IPv6 address.<br>

## What is ME6E
This technology is applied to Ethernet over IPv6 encapsulation using ME6A. Encapsulation is performed to add an IPv6 header to the Ethernet frame, and ME6A is used as the IPv6 address at that time. By using the IPv6 address mapped to IPv6 for L2 communication by Ethernet, it is possible to communicate in the IPv6 space.<br>

Ethernet communication is based on flatting, and pruning is performed by learning, and as a result, unicast communication is realized. ME6E realizes this Ethernet communication by unicast communication by IP routing.<br>

ME6E generates ME6A differently depending on whether or not it advertises the route of ME6E. It is called ME6E-FP when it is advertised by route, and ME6E-PR when it is not advertised by route.<br>

ME6E is an abbreviation for Multiple Ethernet --IPv6 address mapping encapsulation.<br>

### ME6E-FP
ME6E-FP assigns a ME6A-specific prefix to the ME6A IPv6 prefix.<br>

By fixing the ME6A prefix, the ME6A address will be determined if the plane-ID to which the tenant network / L2VPN belongs and the MAC address are determined. In other words, the only settings required for ME6E-FP are the ME6A prefix and its own plane-ID and MAC address information.<br>
The ME6A of the communication partner can be automatically generated from the destination MAC address. Then, the route information to the communication partner is transmitted by the routing protocol. The routing protocol is optional and can be used with OSPFv3, IS-IS, or BGP. Whether realistic or not, it should work with static routing as well.<br>

There is an advantage that the number of settings is small, but since it is necessary to map the route to the MAC address to the IPv6 address and advertise the route, the number of routes for the number of MAC addresses will be added.<br>

ME6E-FP is an abbreviation for Multiple Ethernet --IPv6 address mapping encapsulation --fixed prefix.<br>

### ME6E-PR
ME6E-PR is premised on not advertising IPv6 routes, and by holding the IPv6 prefix of the IPv6 network to which the MAC address is connected in the table, the IPv6 prefix to which the destination MAC address is connected is resolved and ME6A Is what you want. Assuming that IPv6 is available, the image is that Ethernet communication will be added to the route advertisement that realizes IPv6 communication.<br>

Since ME6A route advertisement is not performed, it can be realized by end user initiative, but it is necessary to manage the correspondence between the MAC address and the prefix of the IPv6 address to which the subnet is connected in the table. However, since this correspondence is the same information for the users of that plane, there is room for making settings easier.<br>

ME6E-PR receives IPv6 packets by resolving Neighbor Discovery's Neighbor Advertisement.<br>

For Ethernet frame communication by ME6E-PR, when the address of ME6A corresponding to the MAC address is resolved, ME6E-PR makes a proxy response and receives the IPv6 packet addressed to the corresponding ME6A. After receiving, remove the IPv6 header, decapsulate, take out the Ethernet frame, and send it to the node.<br>

ME6E-PR is an abbreviation for Multiple Ethernet --IPv6 address mapping encapsulation --prefix resolution.<br>

