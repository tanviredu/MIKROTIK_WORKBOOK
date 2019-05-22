## first see there is any ip address or not 



[admin@MikroTik] /ip address> print
Flags: X - disabled, I - invalid, D - dynamic 
 #   ADDRESS            NETWORK         INTERFACE  


my wan network addrerss is 10.42.0.33 gateway = 10.42.0.1 subnet=255.255.255.0


## adding the ip and the gate way 

[admin@MikroTik] /ip address> add address=10.42.0.94/24 interface=ether2 


## adding the gateway 

[admin@MikroTik] /ip route> add gateway=10.42.0.1


## adding the dns

[admin@MikroTik] /ip dns> set servers=8.8.8.8




## testing the connection

[admin@MikroTik] > ping yahoo.com
  SEQ HOST                                     SIZE TTL TIME  STATUS           
    0 72.30.35.10                                56  50 272ms
    1 72.30.35.10                                56  50 268ms
    2 72.30.35.10                                56  50 289ms
    sent=3 received=3 packet-loss=0% min-rtt=268ms avg-rtt=276ms 
   max-rtt=289ms 


## adding the localnetwork with interface 

[admin@MikroTik] /ip address> add address=192.168.88.1/24 interface=ether3 



## adding the tiny core linux as a pc
	
	##giving ip address in the tiny core linux

	gns3@box:/$ sudo ip addr add 192.168.88.100/24 dev eth0

	## adding gateway 
	gns3@box:/$ sudo route add default gw 192.168.88.1




## we can access the local network from pc but to go to the WAN we have to add NAT.

[admin@MikroTik] /ip firewall nat> add chain=srcnat src-address=192.168.88.0/24 
action=masquerade 



## changing the interface name

ether2 ---> ether2-WAN

ether3 ---> ether3-LAN




[admin@MikroTik] /interface> set 2 name=ether2-WAN
[admin@MikroTik] /interface> set 3 name=ether2-LAN  
[admin@MikroTik] /interface> print
Flags: D - dynamic, X - disabled, R - running, S - slave 
 #     NAME                                TYPE       ACTUAL-MTU L2MTU
 0  R  ether1                              ether            1500
 1  R  ether2                              ether            1500
 2  R  ether2-LAN                          ether            1500
 3  R  ether2-WAN                          ether            1500
 4  R  ether5                              ether            1500



## stopping unnessery service

[admin@MikroTik] /interface> /ip service 
[admin@MikroTik] /ip service> print
Flags: X - disabled, I - invalid 
 #   NAME      PORT ADDRESS                                       CERTIFICATE  
 0   telnet      23
 1   ftp         21
 2   www         80
 3   ssh         22
 4 XI www-ssl    443                                               none         
 5   api       8728
 6   winbox    8291
 7   api-ssl   8729                                               none         
[admin@MikroTik] /ip service> disable 1
[admin@MikroTik] /ip service> disable 3
[admin@MikroTik] /ip service> disable 5
[admin@MikroTik] /ip service> disable 7
[admin@MikroTik] /ip service> print
Flags: X - disabled, I - invalid 
 #   NAME      PORT ADDRESS                                       CERTIFICATE  
 0   telnet      23
 1 XI ftp         21
 2   www         80
 3 XI ssh         22
 4 XI www-ssl    443                                               none         
 5 XI api       8728
 6   winbox    8291
 7 XI api-ssl   8729  




## first we remove all the configuration then add a ethernet seitch and then add 3 linux PC 

[admin@MikroTik] /ip service> system reset-configuration


## setting the dhcp server

	1)give ip
	2)gate way
	3)NAT
	4)set the dhcp server


	4)

			[admin@MikroTik] /ip dhcp-server> 
			[admin@MikroTik] /ip dhcp-server> 
			[admin@MikroTik] /ip dhcp-server> setup
			Select interface to run DHCP server on 

			dhcp server interface: ether3
			Select network for DHCP addresses 

			dhcp address space: 192.168.88.0/24
			Select gateway for given network 

			gateway for dhcp network: 192.168.88.1
			Select pool of ip addresses given out by DHCP server 

			addresses to give out: 192.168.88.2-192.168.88.254
			Select DNS servers 

			dns servers: 8.8.8.8
			Select lease time 

			lease time: 10m
			[admin@MikroTik] /ip dhcp-server> 



5)open the tiny core linux and you will see ip is already provided
