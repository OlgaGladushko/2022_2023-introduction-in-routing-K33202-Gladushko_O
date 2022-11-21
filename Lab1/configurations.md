# Текст конфигураций для сетевых устройств
## Центральный роутер (RO1)
/interface vlan 
add interface=ether2 name=vlan10 vlan-id=10 
add interface=ether2 name=vlan20 vlan-id=20 
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool 
add name=pool10 ranges=192.168.10.5-192.168.10.250 
add name=pool20 ranges=192.168.20.5-192.168.20.250 
/ip dhcp-server 
add address-pool=pool10 disabled=no interface=vlan10 name=server10
add address-pool=pool20 disabled=no interface=vlan20 name=server20 
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=192.168.10.1/24 interface=vlan10 network=192.168.10.0 
add address=192.168.20.1/24 interface=vlan20 network=192.168.20.0 
/ip dhcp-client 
add disabled=no interface=ether1 
/ip dhcp-server network 
add address=192.168.10.0/24 gateway=192.168.10.1 
add address=192.168.20.0/24 gateway=192.168.20.1 
/system identity 
set name=RO1

## Коммутатор 1-го уровня (SW01.L3.01)
/interface bridge 
add name=bridge10 
add name=bridge20 
/interface vlan 
add interface=ether2 name=vlan10 vlan-id=10 
add interface=ether2 name=vlan20 vlan-id=20 
add interface=ether3 name=vlan104 vlan-id=10 
add interface=ether4 name=vlan204 vlan-id=20
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port 
add bridge=bridge10 interface=vlan10 
add bridge=bridge20 interface=vlan20 
add bridge=bridge10 interface=vlan104 
add bridge=bridge20 interface=vlan204 
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
/ip dhcp-client 
add disabled=no interface=ether1 
add disabled=no interface=bridge10 
add disabled=no interface=bridge20 
/system identity 
set name=SWO01.L3.01

## Коммутатор 2-го уровня (SW02.L3.01)
/interface bridge 
add name=bridge10 
/interface vlan 
add interface=ether2 name=vlan10 vlan-id=10 
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port 
add bridge=bridge10 interface=vlan10 
add bridge=bridge10 interface=ether3 
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
/ip dhcp-client 
add disabled=no interface=ether1 
add disabled=no interface=bridge10 
/system identity 
set name=SWO02.L3.01

## Коммутатор 2-го уровня (SW02.L3.02)
/interface bridge 
add name=bridge20 
/interface vlan 
add interface=ether2 name=vlan20 vlan-id=20 
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port 
add bridge=bridge20 interface=vlan20 
add bridge=bridge20 interface=ether3 
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
/ip dhcp-client 
add disabled=no interface=ether1 
add disabled=no interface=bridge20 
/system identity 
set name=SWO02.L3.02