# Текст конфигураций для сетевых устройств
## Роутер MSK (RO1.MSK)
```/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool 
add name=pool10 ranges=192.168.10.5-192.168.10.250  
/ip dhcp-server 
add address-pool=pool10 disabled=no interface=ether4 name=server1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.10.1.1/30 interface=ether2 network=10.10.1.0
add address=10.10.2.1/30 interface=ether3 network=10.10.2.0
add address=192.168.10.1/24 interface=ether4 network=192.168.10.0
/ip dhcp-server network 
add address=192.168.10.0/24 gateway=192.168.10.1 
/ip route
add distance=1 dst-address=192.168.20.0/24 gateway=10.10.2.2
add distance=1 dst-address=192.168.30.0/24 gateway=10.10.1.2
/system identity 
set name=RO1.MSK
```

## Роутер FRT (RO1.FRT)
```/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool 
add name=pool20 ranges=192.168.20.5-192.168.20.250  
/ip dhcp-server 
add address-pool=pool20 disabled=no interface=ether4 name=server2
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.10.2.2/30 interface=ether3 network=10.10.2.0
add address=10.10.3.1/30 interface=ether2 network=10.10.3.0
add address=192.168.20.1/24 interface=ether4 network=192.168.20.0
/ip dhcp-client
add disabled=no interface=ether1
/ip dhcp-server network 
add address=192.168.20.0/24 gateway=192.168.20.1 
/ip route
add distance=1 dst-address=192.168.10.0/24 gateway=10.10.2.1
add distance=1 dst-address=192.168.30.0/24 gateway=10.10.3.2
/system identity 
set name=RO1.FRT
```

## Роутер BRL (RO1.BRL)
```/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool 
add name=pool30 ranges=192.168.30.5-192.168.30.250  
/ip dhcp-server 
add address-pool=pool30 disabled=no interface=ether4 name=server3
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.10.1.2/30 interface=ether2 network=10.10.1.0
add address=10.10.3.2/30 interface=ether3 network=10.10.3.0
add address=192.168.30.1/24 interface=ether4 network=192.168.30.0
/ip dhcp-client
add disabled=no interface=ether1
/ip dhcp-server network 
add address=192.168.30.0/24 gateway=192.168.30.1 
/ip route
add distance=1 dst-address=192.168.10.0/24 gateway=10.10.1.1
add distance=1 dst-address=192.168.20.0/24 gateway=10.10.3.1
/system identity 
set name=RO1.BRL
```
