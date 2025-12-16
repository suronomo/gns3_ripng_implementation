# Testing RIPng and Network Services in GNS3

## 1. Assumptions and Objective

The project assumes an emulated network environment in GNS3 using Cisco routers, with RIPng configured as the dynamic routing protocol. A network service, such as DNS, is deployed to test connectivity. The objective is to observe and analyze the behavior of RIPng in an IPv6 network and to verify that network services remain reachable across the routers.

## 2. Test environment and infrastructure
### a) Network topology
![](https://raw.githubusercontent.com/suronomo/gns3_ripng_implementation/main/figures/network_topology_ripng.png)
<div align="center" style="font-size:17px;">
  Figure 1. Network topology implemented in GNS3
</div><br>

<div align="left" style="font-size:17px;">
  Table 1. IPv6 addressing plan of the topology from figure 1
</div><br>

| Device           | Interface          | Address               | Branch |
|-----------------|------------------|---------------------|--------|
| Router-G         | GigabitEthernet1/0 | 2001:db8:1000:A::1  | G      |
| Router-G         | GigabitEthernet0/0 | 2001:db8:2000:A::10 | G      |
| Klient-Windows-G | Ethernet1         | 2001:db8:1000:A::2  | G      |
| Klient-VPC1-G    | Ethernet2         | 2001:db8:1000:A::3  | G      |
| Klient-VPC2-G    | Ethernet3         | 2001:db8:1000:A::4  | G      |
| Klient-VPC3-G    | Ethernet4         | 2001:db8:1000:A::5  | G      |
| Klient-VPC4-G    | Ethernet5         | 2001:db8:1000:A::6  | G      |
| Router-W         | GigabitEthernet1/0 | 2001:db8:2000:C::1  | W      |
| Router-W         | GigabitEthernet0/0 | 2001:db8:1000:C::10 | W      |
| Klient-Windows-W | Ethernet1         | 2001:db8:2000:A::2  | W      |
| Klient-VPC1-W    | Ethernet2         | 2001:db8:2000:A::3  | W      |
| Klient-VPC2-W    | Ethernet3         | 2001:db8:2000:A::4  | W      |
| Klient-VPC3-W    | Ethernet4         | 2001:db8:2000:A::5  | W      |
| Klient-VPC4-W    | Ethernet5         | 2001:db8:2000:A::6  | W      |
| Router-K         | GigabitEthernet1/0 | 2001:db8:1111:B::20 | K      |
| Router-K         | GigabitEthernet0/0 | 2001:db8:1000:C::20 | K      |
| Router-K         | GigabitEthernet2/0 | 2001:db8:4000:E::1  | K      |
| Klient-Windows-K1| Ethernet1         | 2001:db8:4000:C::2  | K      |
| Klient-Windows-K2| Ethernet2         | 2001:db8:4000:C::3  | K      |
| Klient-Windows-K3| Ethernet3         | 2001:db8:4000:C::4  | K      |
| Router-Server    | GigabitEthernet1/0 | 2001:db8:1111:B::10 | Server |
| Router-Server    | GigabitEthernet0/0 | 2001:db8:2000:A::20 | Server |
| Router-Server    | GigabitEthernet2/0 | 2001:db8:3000:D::1  | Server |
| Windows-Server   | Ethernet1         | 2001:db8:3000:D::2  | Server |


### b) Machine specifications
### Local on-premises environment (VirtualBox)
>*Klient-X (f.e., Klient-Windows-G)*
- OS: Windows 7 64-bit Home
- CPU: Allocated 1 vCPU (Intel Core i7-10700K 3.8 GHz)
- RAM: 2 GB
- GPU: 128 MB
- Virtual disk (VHD): 30 GB

>*Windows-Server*
- OS: Windows 10 Server
- CPU: Allocated 1 vCPU (Intel Core i7-10700K 3.8 GHz)
- RAM: 2 GB
- GPU: 128 MB
- Virtual disk (VHD): 40 GB

>*Router-X (f.e., Router-K)*
- Hardware: Cisco C7200
- OS: Cisco IOS version 12.4(24)TS

## 3. Cisco routers configuration
This section presents the configuration of RIPng on Cisco routers.<br>
## Router-G
```text
Configure terminal
Interface gi0/0
Ipv6 add 2001:db8:2000:A::10/64
No shutdown
Interface gi1/0
Ipv6 add 2001:db8:1000:A::1/64
No shutdown
ipv6 unicast-routing
ipv6 router rip network
interface gi0/0
ipv6 rip network enable
interface gi1/0
ipv6 rip network enable
do show ipv6 route
exit
```
## Router-W
```text
Configure terminal
Interface gi0/0
Ipv6 add 2001:db8:1000:C::10/64
No shutdown
Interface gi1/0
Ipv6 add 2001:db8:2000:C::1/64
No shutdown
ipv6 unicast-routing
ipv6 router rip network
interface gi0/0
ipv6 rip network enable
interface gi1/0
ipv6 rip network enable
do show ipv6 route
exit
```
## Router-K
```text
Configure terminal
Interface gi0/0
Ipv6 add 2001:db8:1000:C::20/64
No shutdown
Interface gi1/0
Ipv6 add 2001:db8:1111:B::20/64
No shutdown
Interface gi2/0
Ipv6 add 2001:db8:4000:E::1/64
No shutdown
ipv6 unicast-routing
ipv6 router rip network
interface gi0/0
ipv6 rip network enable
interface gi1/0
ipv6 rip network enable
interface gi2/0
ipv6 rip network enable
do show ipv6 route
exit
```
## Router-Server
```text
Configure terminal
Interface gi0/0
Ipv6 add 2001:db8:2000:A::20/64
No shutdown
Interface gi1/0
Ipv6 add 2001:db8:1111:B::10/64
No shutdown
Interface gi2/0
Ipv6 add 2001:db8:3000:D::1/64
No shutdown
ipv6 unicast-routing
ipv6 router rip network
interface gi0/0
ipv6 rip network enable
interface gi1/0
ipv6 rip network enable
interface gi2/0
ipv6 rip network enable
do show ipv6 route
exit
```
## 4. Verification of RIPng and network services
![](https://raw.githubusercontent.com/suronomo/gns3_ripng_implementation/main/figures/ping_wing_kwink1.png)
<div align="center" style="font-size:17px;">
  Figure 2. Network Traffic Analysis – Ping from Klient-Windows-G to Klient-Windows-K1
</div><br>

Network connectivity was also verified by sending queries to the DNS server (site: `serwer-firmowy.pl`) on the `Windows-server` machine from `Klient-Windows-G`.<br>

![](https://raw.githubusercontent.com/suronomo/gns3_ripng_implementation/main/figures/dns_server_www.png)
<div align="center" style="font-size:17px;">
  Figure 3. Test web page hosted on Windows-Server machine
</div><br>

![](https://raw.githubusercontent.com/suronomo/gns3_ripng_implementation/main/figures/dns_server_www_ping.png)
<div align="center" style="font-size:17px;">
  Figure 4. Network Traffic Analysis – DNS-related ping from Klient-Windows-G to Windows-Server
</div><br>

## 5. Conclusion

The applied RIPng configuration ensured proper route propagation without manual static routing, which simplified network management in the multi-segment topology. Connectivity tests, including ICMP echo requests and DNS-related queries, confirmed that end devices in different network locations were able to communicate reliably with services hosted in the DMZ.

The successful operation of network services such as DNS and a test web server demonstrates that RIPng is sufficient for small to medium-sized IPv6 networks where simplicity and ease of configuration are prioritized. The results confirm that the designed topology, IPv6 addressing scheme, and routing configuration meet the assumed project objectives and validate the correctness of the implemented solution.

## 6. Limitations
- **Limited hop count** – RIPng supports a maximum of 15 hops, which restricts its use in larger or more complex network topologies.
- **Slow convergence** – as a distance-vector routing protocol, RIPng converges slower after topology changes compared to link-state protocols.
- **Limited scalability** – due to periodic route updates and hop-count-based metrics, RIPng is suitable mainly for small to medium-sized IPv6 networks.
- **Lack of advanced routing policies** – RIPng does not provide native mechanisms for fine-grained traffic control, such as advanced routing policies or fast failover capabilities.
