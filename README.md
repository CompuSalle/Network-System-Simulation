# Network System Simulation Using Cisco Packet Tracer

### Before Using Please Refer To [LICENSE](https://github.com/WassemAdil/Network-System-Simulation/blob/main/LICENSE)

-----------------------
Use the original Cisco Packet Tracer Program From [Cisco](https://www.netacad.com/courses/packet-tracer)

> Notes

If Ping Command Bettwen Connected Computers 'Not Isolated Computers' is not working this is duo to a bug in CISCO PACKET TRACER. 

To sole this issue do the follow :

1. Know that connection over the routers wont work until we redo the crypto map. **Please** go to router(R-B1) and router(R-B2) and type in both :
    - enable
    - interface gigabitEthernet 0/0
    - no crypto map VPN-MAP
    - crypto map VPN-MAP
    - (exit and save changes PLEASE)

Now ping command should work accros routers.

------------------------------------------------------

Programe Screenshot

![test](https://github.com/WassemAdil/Network-System-Simulation/blob/main/Program_Screenshot.png)

----------------------------------------------

### Each Step Used Commands To Achive

- Router 1 = R-B1
- Router 2 = R-B2
- Router 3 = Tunnel Router

-------------------------------------------

> VLAN

**IN SWITCH that we want to create a VLAN**

    enable 
    configure terminal 
    vlan 2
    name (name of vlan)
    exit(save)
    configure terminal 
    interface  fastEthernet 0/1
    switchport mode access 
    switchport access vlan 2 
	

**Connecting switches Together**

	enable
	configure terminal 
	interface gigabitEthernet 0/1
	switchport mode trunk 

---------------------------------

> ACL

**Go To The Router That You Shall Create ACL On**

	enable 
	configure terminal
	access-list 101 deny icmp 192.168.1.1 0.0.0.0 10.0.0.0 0.255.255.255
	access-list 101 permit ip any any 
	save 

**After Saving Type**

	enable 
	configure terminal
	interface gigabitEthernet 0/0
	ip access-group 101 out + in 

------------------------------------

> VPN


**Router 1**

    enable 
    configure terminal 
    interface tunnel 10 
    ip add 172.1.1.1 255.255.255.0 (NEW IP)
    tunnel source gigabitEthernet 0/0 (ROUTER b/w)
    tunnel destination 192.168.3.2 
    no shutdown 

**Router 2**

    enable 
    configure terminal 
    interface tunnel 20 
    ip add 172.1.1.2 255.255.255.0 
    tunnel source gigabitEthernet 0/0 (ROUTER b/w)
    tunnel destination 192.168.2.2
    no shutdown 

**Router 1**

	enable 
	configure terminal
	(Computer networkin other side IP) + tunnel ip router 2
	ip route 10.1.1.0 255.0.0.0 172.1.1.2
	save

**Router 2**

	enable 
	configure terminal 
	(Computer network in other side IP ) + tunnel ip router 1
	ip route 192.168.1.0 255.255.255.0 172.1.1.1
	save 

-------------------------------------------

> IPSEC

**Router 1**

	enable 
	configure terminal 
	license boot module c1900 technology-package securityk9 
		(YES)
	save
	reload (To rebot)


**Router 3**
		
	enable 
	configure terminal 
	license boot module c1900 technology-package securityk9 
		(YES)
	save
	reload (To rebot)


**Router 1**

		At (Global#)
			access-list 110 permit ip any any
			crypto isakmp policy 10
			encryption aes 256
			authentication pre-share 
			group 5
			exit
			crypto isakmp key thekey address 192.168.3.2
			
				crypto ipsec transform-set VPN-SET esp-aes esp-sha-hmac
				crypto map VPN-MAP 10 ipsec-isakmp
				set peer 192.168.3.2
				set transform-set VPN-SET
				match address 110

**Router 3**

		At(Global#)
			access-list 110 permit ip any any
			crypto isakmp policy 10
			encryption aes 256
			authentication pre-share 
			group 5
			exit
			crypto isakmp key thekey address 192.168.2.2
			
				crypto ipsec transform-set VPN-SET esp-aes esp-sha-hmac
				crypto map VPN-MAP 10 ipsec-isakmp
				set peer 192.168.2.2
				set transform-set VPN-SET
				match address 110

**Both 1 and 3**

	enable
	configure terminal 
	interface gigabitEthernet 0/0
	crypto map VPN-MAP

 


