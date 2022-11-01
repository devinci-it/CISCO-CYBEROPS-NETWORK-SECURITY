
<!--HTTPS://DAVINCI-IT.GITHUB.IO-->
<!--READme.md TEMPLATE-->
---
# __NETWORK SECURITY | Skills Integration Challenge__

![image](https://img.shields.io/badge/CISCO|PACKET_TRACER-0078D6?style=for-the-badge&logo=cisco&logoColor=white)
![image](https://img.shields.io/badge/Markdown-ffffff?style=for-the-badge&logo=markdown&logoColor=black)

---
### __CONNECT WITH ME:__
<p align='left'>
<a href='https://twitter.com/It_vince01'>
<img src="https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white" alt="twitter_link"/></a>
<a href='https://www.linkedin.com/in/vincent-de-torres-854612240/'>
<img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" alt="linkedin"/></a>
<a href='https://ko-fi.com/devinci'>
<img src="https://img.shields.io/badge/Ko--fi-F16061?style=for-the-badge&logo=ko-fi&logoColor=white" alt="kofi_link"/></a>
</p>

---

### __OVERVIEW__

---
This culminating activity includes many of the skills that you have acquired during this course. The routers and switches are preconfigured with the basic device settings, such as IP addressing and routing. You will secure routers using the CLI to configure various IOS features, including AAA, SSH, and Zone-Based Policy Firewall (ZPF). You will configure a site-to-site VPN between R1 and R3. You will secure the switches on the network. In addition, you will also configure firewall functionality on the ASA.

---

### __OBJECTIVES__

- [Configure basic router security](##Configure Basic Router Security)
- [Configure Switch Security](##Configure Basic Switch Security)
- [Configure AAA Local Authentication](##Configure AAA Local Authentication)
- [Configure SSH](##Configure SSH)
- [Secure Against Login Attacks](##Secure Against Login Attacks)
- [Configure Site-to-Site IPsec VPNs](##Configure Site-to-Site IPsec VPNs)
- [Configure Firewall and IPS Settings](##Configure Firewall and IPS Settings)
- [Configure ASA Basic Security and Firewall Settings](##Configure ASA Basic Security and Firewall Settings)
---
```
Note: Not all security features will be configured on all devices, however, they would be in a production network.
```
---
#### FILES
- [PACKET TRACER ACTIVITY (.pkt)](/)
- [PACKET TRACER ACTIVITY_KEY(.pkt)](/)
## __TOPOLOGY__
![topology](/assets/topology.png)

---

## __Configure Basic Router Security__
### __R1__
```
conf t
security passwords min-length 10
enable secret ciscoenapa55
service password-encryption
line console 0
password ciscoconpa55
exec-timeout 15 0
login
logging synchronous
banner motd $Unauthorized access strictly prohibited and prosecuted to the full 
extent of the law!$
end
```


### __R2__
```
conf t
enable secret ciscoenapa55
line vty 0 4
password ciscovtypa55
exec-timeout 15 0
login
end
```
---

## __Configure Switch Security__

### __S1__

```
conf t
service password-encryption
enable secret ciscoenapa55
line console 0 
password ciscoconpa55
exec-timeout 5 0
login
logging synchronous
line vty 0 15
password ciscovtypa55
exec-timeout 5 0
login
banner motd $Unauthorized access strictly prohibited and prosecuted to the full 
extent of the law!$
end
```

### S1 and S2
#### Trunking
```
conf t
interface FastEthernet 0/1
switchport mode trunk
switchport trunk native vlan 99
switchport nonegotiate
end
```


### S1 
#### Port Security

```
conf t
interface FastEthernet 0/6
switchport mode access
spanning-tree portfast
spanning-tree bpduguard enable
shutdown
switchport port-security
switchport port-security mac-address sticky
no shutdown
interface range f0/2 – 5 , f0/7 – 24 , g0/1 - 2
shutdown
end
```
---

## __Configure AAA Local Authentication__
### R1

```
conf t
username Admin01 privilege 15 secret Admin01pa55
aaa new-model
aaa authentication login default local enable
end
```

---
## __Configure SSH__
### __R1__

```
conf t
ip domain-name ccnasecurity.com
crypto key generate rsa
1024
ip ssh version 2
line vty 0 4
transport input ssh
end
```

---
## __Secure Against Login Attacks__
### __R1__
```
conf t
login block-for 60 attempts 2 within 30
login on-failure log
```

---
## __Configure Site-to-Site IPsec VPNs__

### __R1__

```
conf t
access-list 101 permit ip 172.20.1.0 0.0.0.255 172.30.3.0 0.0.0.255
crypto isakmp policy 10
encryption aes 256
authentication pre-share
hash sha
group 5
lifetime 3600
exit
crypto isakmp key ciscovpnpa55 address 10.20.20.1
crypto ipsec transform-set VPN-SET esp-aes 256 esp-sha-hmac
crypto map CMAP 10 ipsec-isakmp
set peer 10.20.20.1
set pfs group5
set transform-set VPN-SET
match address 101
exit
interface S0/0/0
crypto map CMAP
end
```


### __R3__

```
access-list 101 permit ip 172.30.3.0 0.0.0.255 172.20.1.0 0.0.0.255
crypto isakmp policy 10
encryption aes 256
authentication pre-share
hash sha 
group 5
lifetime 3600
exit
crypto isakmp key ciscovpnpa55 address 10.10.10.1
crypto ipsec transform-set VPN-SET esp-aes 256 esp-sha-hmac
crypto map CMAP 10 ipsec-isakmp
set peer 10.10.10.1
set transform-set VPN-SET
match address 101
exit
interface S0/0/1
crypto map CMAP
end
```

---

## __Configure Firewall and IPS Settings__

### __R3__

```
!FW CONFIGURATION
zone security IN-ZONE
zone security OUT-ZONE
access-list 110 permit ip 172.30.3.0 0.0.0.255 any
access-list 110 deny ip any any
class-map type inspect match-all INTERNAL-CLASS-MAP
match access-group 110
exit
policy-map type inspect IN-2-OUT-PMAP
class type inspect INTERNAL-CLASS-MAP
inspect
zone-pair security IN-2-OUT-ZPAIR source IN-ZONE destination OUT-ZONE
service-policy type inspect IN-2-OUT-PMAP
exit
interface g0/1
zone-member security IN-ZONE
exit
interface s0/0/1
zone-member security OUT-ZONE
end
```
```
!IPS configs
mkdir ipsdir
conf t
ip ips config location flash:ipsdir
ip ips name IPS-RULE
ip ips signature-category
category all
retired true
exit 
category ios_ips basic
retired false
exit
! 
interface s0/0/1
ip ips IPS-RULE in
```

## __Configure ASA Basic Security and Firewall Settings__

### __CCNAS_ASA__

```
enable
conf t
interface vlan 1
nameif inside
security-level 100
ip address 192.168.10.1 255.255.255.0
interface vlan 2
nameif outside
security-level 0
no ip address dhcp
ip address 209.165.200.234 255.255.255.248
exit
hostname CCNAS-ASA
domain-name ccnasecurity.com
enable password ciscoenapa55
username admin password adminpa55
aaa authentication ssh console LOCAL
ssh 192.168.10.0 255.255.255.0 inside
ssh 172.30.3.3 255.255.255.255 outside
ssh timeout 10
dhcpd address 192.168.10.5-192.168.10.30 inside
dhcpd enable inside
route outside 0.0.0.0 0.0.0.0 209.165.200.233
object network inside-net
subnet 192.168.10.0 255.255.255.0
nat (inside,outside) dynamic interface
exit
conf t
class-map inspection_default
match default-inspection-traffic
exit
policy-map global_policy
class inspection_default
inspect icmp
exit
service-policy global_policy global
```
