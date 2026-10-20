collapsed core design

===

I built a collapsed core topology in the Sandbox implementing the following:



* Trunking, native, access and switch vlan interface
* DHCP
* DNS
* EtherChannel
* Spanning-tree priority
* HSRP
* Floating static routing
* SSH
* OSPF
* Access-list



I started laying out the topology first put PCs for each departments and access switches. Upon

arranging the topology I found out that there is no IP phone as included in the devices option

so I made this lab a little simpler from the access switch side. There is also one server to

to setup a DNS service later on this lab.

When I was done with the first layer of the architecture I moved on setting up core switches. In

This part I connected each network devices including the main-router and isp leaving me a partial

mesh design as I finish my topology. After this I put all switches into rapid-pvst+ for faster

service.

I assigned IP to the SVI including their standby IP, set core switches to be active on hsrp

taking vlan priority 0 and vice versa. I also included a management vlan for access switches

since I will setup ssh remote access later.

Here are their classifications:

vlan 10 IT

192.168.10.0/24

vip: 192.168.10.1

core 1: 192.168.10.2

core 2: 192.168.10.3



vlan 20 HR

192.168.20.0/24

vip: 192.168.20.1

core 1: 192.168.20.2

core 2: 192.168.20.3





vlan 30 STAFF

192.168.30.0/24

vip: 192.168.30.1

core 1: 192.168.30.2

core 2: 192.168.30.3





vlan 40 SERVER

192.168.40.0/24

vip: 192.168.40.1

core 1: 192.168.40.2

core 2:192.168.40.3





management vlan 99

192.168.50.0/24

vip: sw1: 192.168.50.1

core1: 192.168.50.2

core2:192.168.50.3

sw1: 192.168.50.10

sw2: 192.168.50.20

sw3: 192.168.50.30

sw4: 192.168.50.40

core1:

active 110 \& vlan prio 0:



preempt

vlan99 5

vlan10 1

vlan20 2

standby vlan prio 4096:

vlan30 3

vlan40 4



core2

active 110 \& vlan prio 0:



preempt

vlan30 3

vlan40 4

standby vlan prio 4096:

vlan99 5

vlan10 1

vlan20 2

Once I was done with all the setup I mentioned above. I proceed on putting IP on interfaces
mainly core switches and main router. I need to ensure that all network devices connects or

communicates with each other before configuring main-router to isp connections. Included
loopback addresses. I added OSPF connection routes with this putting all vlan to passive-

interfaces but include their ip on the network configs. Set main-router connections to

core switches as point-to-point and give it as the highest priority to set it as a

default designated router, leaving the connected layer 3 Ethechannel to be in a 2 way form.

Here are their classifications:

main-router

loopback0:

192.168.255.255



core 1

loopback0:

192.168.254.254



core 2

loopback0:

192.168.253.253

main-router - core1

192.168.60.0/30



main-router - core2

192.168.61.0/30



core1-core2

192.168.62.0/30



Upon checking that all devices are able to ping with each other I enabled the DNS service on

in the server. I initiate a default-information originate to the 2 connection on the isp leaving

one interface as floating static to ensure redundancy. It took me a long time to figure this

out since the route for both networks main-router to isp is 0.0.0.0 0.0.0.0 (next-hop) when

I ping from the PC it translates the name to an IP but TTL expires using traceroute I noticed

that the packet is bouncing into one another allowing me not to receive the packet.
What I did was configure loopback addresses of what are inside the server. This solved the

problem and enabled my topology to somehow translates a real life connection of networks.

Lastly, I configured the ssh, login local, secret into all network devices except for the

isp. This can only be access by the IT department and no one else.

Here are the running commands of each devices:

These are the running configs from each of my network devices:

SW1
SW1#sh run

Building configuration...



Current configuration : 3522 bytes

!

! Last configuration change at 07:54:43 UTC Mon Oct 19 2026

!

version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

!

hostname SW1

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$7j5y$w1wj/UBfVNVUH10rPVkBA1

!

username admin secret 5 $1$8.YI$9cNnBPF/CBc/jCWnCTLAh/

no aaa new-model

!

!

!

!

!

!

!

!

ip domain-name mydomain.com

no ip cef

no ipv6 cef

!

!

!

spanning-tree mode rapid-pvst

spanning-tree portfast edge default

spanning-tree extend system-id

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface FastEthernet0/1

&#x20;switchport access vlan 10

&#x20;switchport mode access

!

interface FastEthernet0/2

&#x20;switchport access vlan 10

&#x20;switchport mode access

!

interface FastEthernet0/3

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/4

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/5

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/6

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/7

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/8

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/9

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/10

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/11

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/12

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/13

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/14

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/15

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/16

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/17

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/18

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/19

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/20

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/21

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/22

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/23

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/24

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface GigabitEthernet0/1

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface GigabitEthernet0/2

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface Vlan1

&#x20;no ip address

&#x20;shutdown

!

interface Vlan99

&#x20;ip address 192.168.50.10 255.255.255.0

!

ip default-gateway 192.168.50.1

ip forward-protocol nd

!

ip http server

ip http secure-server

!

ip ssh version 2

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

!

!

!

!

!

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

control-plane

!

line con 0

line aux 0

line vty 0 4

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

!

end

SW2

SW2#sh ru

Building configuration...



Current configuration : 3522 bytes

!

! Last configuration change at 07:55:17 UTC Mon Oct 19 2026

!

version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

!

hostname SW2

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$JDLs$j8HKsw1SLlSp97WOs15wo.

!

username admin secret 5 $1$KUn4$PN/jB.3sbTSuC0XjyoXT90

no aaa new-model

!

!

!

!

!

!

!

!

ip domain-name mydomain.com

no ip cef

no ipv6 cef

!

!

!

spanning-tree mode rapid-pvst

spanning-tree portfast edge default

spanning-tree extend system-id

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface FastEthernet0/1

&#x20;switchport access vlan 20

&#x20;switchport mode access

!

interface FastEthernet0/2

&#x20;switchport access vlan 20

&#x20;switchport mode access

!

interface FastEthernet0/3

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/4

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/5

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/6

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/7

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/8

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/9

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/10

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/11

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/12

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/13

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/14

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/15

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/16

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/17

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/18

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/19

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/20

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/21

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/22

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/23

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/24

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface GigabitEthernet0/1

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface GigabitEthernet0/2

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface Vlan1

&#x20;no ip address

&#x20;shutdown

!

interface Vlan99

&#x20;ip address 192.168.50.20 255.255.255.0

!

ip default-gateway 192.168.50.1

ip forward-protocol nd

!

ip http server

ip http secure-server

!

ip ssh version 2

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

!

!

!

!

!

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

control-plane

!

line con 0

line aux 0

line vty 0 4

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

!

end

SW3



SW3#sh run

Building configuration...



Current configuration : 3522 bytes

!

! Last configuration change at 07:55:44 UTC Mon Oct 19 2026

!

version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

!

hostname SW3

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$.uae$aDHwzMuRP3TrApB7AEXZa0

!

username admin secret 5 $1$/9uo$7/h0dA1yAl3DqsbFb8yoZ/

no aaa new-model

!

!

!

!

!

!

!

!

ip domain-name mydomain.com

no ip cef

no ipv6 cef

!

!

!

spanning-tree mode rapid-pvst

spanning-tree portfast edge default

spanning-tree extend system-id

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface FastEthernet0/1

&#x20;switchport access vlan 30

&#x20;switchport mode access

!

interface FastEthernet0/2

&#x20;switchport access vlan 30

&#x20;switchport mode access

!

interface FastEthernet0/3

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/4

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/5

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/6

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/7

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/8

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/9

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/10

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/11

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/12

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/13

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/14

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/15

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/16

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/17

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/18

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/19

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/20

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/21

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/22

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/23

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/24

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface GigabitEthernet0/1

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface GigabitEthernet0/2

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface Vlan1

&#x20;no ip address

&#x20;shutdown

!

interface Vlan99

&#x20;ip address 192.168.50.30 255.255.255.0

!

ip default-gateway 192.168.50.1

ip forward-protocol nd

!

ip http server

ip http secure-server

!

ip ssh version 2

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

!

!

!

!

!

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

control-plane

!

line con 0

line aux 0

line vty 0 4

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

!

end

SW4



SW4#sh run

Building configuration...



Current configuration : 2963 bytes

!

! Last configuration change at 07:56:23 UTC Mon Oct 19 2026

!

version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

!

hostname SW4

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$QIOL$z09ZvzgRINPGP4k3ibn6q1

!

username admin secret 5 $1$Ri0Y$0whwcJ1pYHLz/qrlRdBJ6/

no aaa new-model

!

!

!

!

!

!

!

!

ip domain-name mydomain.com

no ip cef

no ipv6 cef

!

!

!

spanning-tree mode rapid-pvst

spanning-tree portfast edge default

spanning-tree extend system-id

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface FastEthernet0/1

&#x20;switchport access vlan 40

&#x20;switchport mode access

!

interface FastEthernet0/2

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/3

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/4

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/5

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/6

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/7

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/8

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/9

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/10

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/11

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/12

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/13

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/14

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/15

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/16

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/17

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/18

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/19

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/20

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/21

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/22

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/23

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/24

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface GigabitEthernet0/1

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface GigabitEthernet0/2

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface Vlan1

&#x20;no ip address

&#x20;shutdown

!

interface Vlan99

&#x20;ip address 192.168.50.40 255.255.255.0

!

ip default-gateway 192.168.50.1

ip forward-protocol nd

!

ip http server

ip http secure-server

!

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

!

!

!

!

!

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

control-plane

!

line con 0

line aux 0

line vty 0 4

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

!

end

core2

core2#sh run

Building configuration...



Current configuration : 5241 bytes

!

! Last configuration change at 07:56:51 UTC Mon Oct 19 2026

!

version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

!

hostname core2

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$Zuf0$vXhWoJiyZFstGcqkyQ2qH.

!

username admin secret 5 $1$a96N$EIAdqpCMFIYfAMXwuEP1H/

no aaa new-model

!

!

!

!

!

ip routing

!

!

!

ip domain-name mydomain.com

ip cef

no ipv6 cef

!

!

!

spanning-tree mode rapid-pvst

spanning-tree portfast edge default

spanning-tree extend system-id

spanning-tree vlan 10,20,99 priority 4096

spanning-tree vlan 30,40 priority 0

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface Loopback0

&#x20;ip address 192.168.253.253 255.255.255.255

!

interface Port-channel1

&#x20;no switchport

&#x20;ip address 192.168.62.1 255.255.255.252

!

interface FastEthernet0/1

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/2

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/3

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/4

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/5

&#x20;no switchport

&#x20;no ip address

&#x20;channel-group 1 mode active

!

interface FastEthernet0/6

&#x20;no switchport

&#x20;no ip address

&#x20;channel-group 1 mode active

!

interface FastEthernet0/7

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/8

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/9

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/10

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/11

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/12

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/13

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/14

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/15

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/16

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/17

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/18

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/19

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/20

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/21

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/22

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/23

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface FastEthernet0/24

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

!

interface GigabitEthernet0/1

&#x20;no switchport

&#x20;ip address 192.168.61.2 255.255.255.252

&#x20;ip ospf network point-to-point

&#x20;negotiation auto

!

interface GigabitEthernet0/2

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

&#x20;negotiation auto

!

interface Vlan1

&#x20;no ip address

&#x20;shutdown

!

interface Vlan99

&#x20;ip address 192.168.50.3 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 5 ip 192.168.50.1

!

interface Vlan10

&#x20;ip address 192.168.10.3 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 1 ip 192.168.10.1

!

interface Vlan20

&#x20;ip address 192.168.20.3 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 2 ip 192.168.20.1

!

interface Vlan30

&#x20;ip address 192.168.30.3 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 3 ip 192.168.30.1

&#x20;standby 3 priority 110

&#x20;standby 3 preempt

!

interface Vlan40

&#x20;ip address 192.168.40.3 255.255.255.0

&#x20;standby version 2

&#x20;standby 4 ip 192.168.40.1

&#x20;standby 4 priority 110

&#x20;standby 4 preempt

!

router ospf 1

&#x20;router-id 192.168.253.253

&#x20;passive-interface Vlan10

&#x20;passive-interface Vlan20

&#x20;passive-interface Vlan30

&#x20;passive-interface Vlan40

&#x20;passive-interface Vlan99

&#x20;passive-interface Loopback0

&#x20;network 192.168.10.0 0.0.0.255 area 0

&#x20;network 192.168.20.0 0.0.0.255 area 0

&#x20;network 192.168.30.0 0.0.0.255 area 0

&#x20;network 192.168.40.0 0.0.0.255 area 0

&#x20;network 192.168.50.0 0.0.0.255 area 0

&#x20;network 192.168.61.0 0.0.0.3 area 0

&#x20;network 192.168.62.0 0.0.0.3 area 0

&#x20;network 192.168.253.253 0.0.0.0 area 0

!

ip forward-protocol nd

!

ip http server

ip http secure-server

!

ip ssh version 2

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

!

!

!

!

!

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

control-plane

!

line con 0

line aux 0

line vty 0 4

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

!

end

core1



core1#sh run

Building configuration...



Current configuration : 4852 bytes

!

! Last configuration change at 07:57:14 UTC Mon Oct 19 2026

!

version 15.2

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

service compress-config

!

hostname core1

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$vs/Q$ttgbJo6T5LSuDFCBAOnGw.

!

username admin secret 5 $1$ubiW$PSL8RrgaaTVJluQTDFKj81

no aaa new-model

!

!

!

!

!

ip routing

!

!

!

ip domain-name mydomain.com

ip cef

no ipv6 cef

!

!

!

spanning-tree mode rapid-pvst

spanning-tree portfast edge default

spanning-tree extend system-id

spanning-tree vlan 10,20,99 priority 0

spanning-tree vlan 30,40 priority 4096

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface Loopback0

&#x20;ip address 192.168.254.254 255.255.255.255

!

interface Port-channel1

&#x20;no switchport

&#x20;ip address 192.168.62.2 255.255.255.252

!

interface FastEthernet0/1

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/2

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/3

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/4

&#x20;switchport trunk allowed vlan 10,20,30,40,99

&#x20;switchport trunk encapsulation dot1q

&#x20;switchport trunk native vlan 1000

&#x20;switchport mode trunk

!

interface FastEthernet0/5

&#x20;no switchport

&#x20;no ip address

&#x20;channel-group 1 mode active

!

interface FastEthernet0/6

&#x20;no switchport

&#x20;no ip address

&#x20;channel-group 1 mode active

!

interface FastEthernet0/7

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/8

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/9

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/10

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/11

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/12

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/13

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/14

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/15

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/16

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/17

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/18

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/19

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/20

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/21

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/22

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/23

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface FastEthernet0/24

&#x20;switchport access vlan 88

&#x20;shutdown

!

interface GigabitEthernet0/1

&#x20;no switchport

&#x20;ip address 192.168.60.2 255.255.255.252

&#x20;ip ospf network point-to-point

&#x20;negotiation auto

!

interface GigabitEthernet0/2

&#x20;switchport access vlan 88

&#x20;switchport mode access

&#x20;shutdown

&#x20;negotiation auto

!

interface Vlan1

&#x20;no ip address

&#x20;shutdown

!

interface Vlan99

&#x20;ip address 192.168.50.2 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 5 ip 192.168.50.1

&#x20;standby 5 priority 110

&#x20;standby 5 preempt

!

interface Vlan10

&#x20;ip address 192.168.10.2 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 1 ip 192.168.10.1

&#x20;standby 1 priority 110

&#x20;standby 1 preempt

!

interface Vlan20

&#x20;ip address 192.168.20.2 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 2 ip 192.168.20.1

&#x20;standby 2 priority 110

&#x20;standby 2 preempt

!

interface Vlan30

&#x20;ip address 192.168.30.2 255.255.255.0

&#x20;ip helper-address 192.168.255.255

&#x20;standby version 2

&#x20;standby 3 ip 192.168.30.1

!

interface Vlan40

&#x20;ip address 192.168.40.2 255.255.255.0

&#x20;standby version 2

&#x20;standby 4 ip 192.168.40.1

!

router ospf 1

&#x20;router-id 192.168.254.254

&#x20;passive-interface Loopback0

&#x20;passive-interface Vlan10

&#x20;passive-interface Vlan20

&#x20;passive-interface Vlan30

&#x20;passive-interface Vlan40

&#x20;passive-interface Vlan99

&#x20;network 192.168.10.0 0.0.0.255 area 0

&#x20;network 192.168.20.0 0.0.0.255 area 0

&#x20;network 192.168.30.0 0.0.0.255 area 0

&#x20;network 192.168.40.0 0.0.0.255 area 0

&#x20;network 192.168.50.0 0.0.0.255 area 0

&#x20;network 192.168.60.0 0.0.0.3 area 0

&#x20;network 192.168.62.0 0.0.0.3 area 0

&#x20;network 192.168.254.254 0.0.0.0 area 0

!

ip forward-protocol nd

!

ip http server

ip http secure-server

!

ip ssh version 2

ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr

ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr

!

!

!

!

!

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

control-plane

!

line con 0

line aux 0

line vty 0 4

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

!

end

main-router

main-router#sh run

Building configuration...



Current configuration : 2607 bytes

!

! Last configuration change at 07:57:39 UTC Mon Oct 19 2026

!

version 15.9

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

!

hostname main-router

!

boot-start-marker

boot-end-marker

!

!

!

enable secret 5 $1$CErh$mIwbEVWqnXDHY58HjtuEv/

!

ip dhcp excluded-address 192.168.10.1 192.168.10.10

ip dhcp excluded-address 192.168.20.1 192.168.20.10

ip dhcp excluded-address 192.168.30.1 192.168.30.10

ip dhcp excluded-address 192.168.50.1 192.168.50.10

!

ip dhcp pool vlan10

&#x20;network 192.168.10.0 255.255.255.0

&#x20;default-router 192.168.10.1

&#x20;dns-server 192.168.40.4

&#x20;domain-name mydomain.com

ip dhcp pool vlan20

&#x20;network 192.168.20.0 255.255.255.0

&#x20;default-router 192.168.20.1

&#x20;dns-server 192.168.40.4

&#x20;domain-name mydomain.com

ip dhcp pool vlan30

&#x20;network 192.168.30.0 255.255.255.0

&#x20;default-router 192.168.30.1

&#x20;dns-server 192.168.40.4

&#x20;domain-name mydomain.com

ip dhcp pool vlan99

&#x20;network 192.168.50.0 255.255.255.0

&#x20;default-router 192.168.50.1

&#x20;dns-server 192.168.40.4

&#x20;domain-name mydomain.com

!

no aaa new-model

!

!

!

mmi polling-interval 60

no mmi auto-configure

no mmi pvc

mmi snmp-timeout 180

!

!

!

!

!

!

!

!

!

!

!

ip domain name mydomain.com

ip cef

no ipv6 cef

!

multilink bundle-name authenticated

!

!

!

!

username admin secret 5 $1$BzXX$8s5K1yz6IrjAHrSSbKoOe1

!

redundancy

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface Loopback0

&#x20;ip address 192.168.255.255 255.255.255.255

!

interface GigabitEthernet0/0

&#x20;ip address 192.168.60.1 255.255.255.252

&#x20;ip ospf network point-to-point

&#x20;ip ospf priority 255

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

interface GigabitEthernet0/1

&#x20;ip address 192.168.61.1 255.255.255.252

&#x20;ip ospf network point-to-point

&#x20;ip ospf priority 255

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

interface GigabitEthernet0/2

&#x20;ip address 10.0.0.2 255.255.255.252

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

interface GigabitEthernet0/3

&#x20;ip address 10.0.0.6 255.255.255.252

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

router ospf 1

&#x20;router-id 192.168.255.255

&#x20;passive-interface Loopback0

&#x20;network 192.168.60.0 0.0.0.3 area 0

&#x20;network 192.168.61.0 0.0.0.3 area 0

&#x20;network 192.168.255.255 0.0.0.0 area 0

&#x20;default-information originate

!

ip forward-protocol nd

!

!

no ip http server

no ip http secure-server

ip route 0.0.0.0 0.0.0.0 10.0.0.1

ip route 0.0.0.0 0.0.0.0 10.0.0.5 2

!

access-list 1 permit 192.168.10.0 0.0.0.255

!

ipv6 ioam timestamp

!

!

!

control-plane

!

line con 0

line aux 0

line vty 0 4

&#x20;login

&#x20;transport input none

line vty 0 15

&#x20;access-class 1 in

&#x20;login local

&#x20;transport input ssh

!

no scheduler allocate

!

end

isp

isp#sh run

Building configuration...



Current configuration : 1512 bytes

!

version 15.9

service timestamps debug datetime msec

service timestamps log datetime msec

no service password-encryption

!

hostname isp

!

boot-start-marker

boot-end-marker

!

!

!

ip dns server

!

ip name-server 67.67.67.67

!

no aaa new-model

!

!

!

mmi polling-interval 60

no mmi auto-configure

no mmi pvc

mmi snmp-timeout 180

!

!

!

!

!

!

!

!

!

!

!

ip domain name mydomain.com

ip cef

no ipv6 cef

!

multilink bundle-name authenticated

!

!

!

!

!

redundancy

!

!

!

!

!

!

!

!

!

!

!

!

!

!

!

interface Loopback0

&#x20;description mydomain.com

&#x20;ip address 67.67.67.67 255.255.255.255

&#x20;ip ospf 1 area 0

!

interface Loopback1

&#x20;description google.com

&#x20;ip address 8.8.8.8 255.255.255.255

&#x20;ip ospf 1 area 0

!

interface Loopback2

&#x20;description pingmynetwork.com

&#x20;ip address 172.66.40.66 255.255.255.255

&#x20;ip ospf 1 area 0

!

interface GigabitEthernet0/0

&#x20;no ip address

&#x20;shutdown

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

interface GigabitEthernet0/1

&#x20;no ip address

&#x20;shutdown

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

interface GigabitEthernet0/2

&#x20;ip address 10.0.0.1 255.255.255.252

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

interface GigabitEthernet0/3

&#x20;ip address 10.0.0.5 255.255.255.252

&#x20;duplex auto

&#x20;speed auto

&#x20;media-type rj45

!

router ospf 1

!

ip forward-protocol nd

!

!

no ip http server

no ip http secure-server

ip route 0.0.0.0 0.0.0.0 10.0.0.2

ip route 0.0.0.0 0.0.0.0 10.0.0.6 2

!

ipv6 ioam timestamp

!

!

!

control-plane

!

line con 0

line aux 0

line vty 0 4

&#x20;login

&#x20;transport input none

!

no scheduler allocate

!

end



