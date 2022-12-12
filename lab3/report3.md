University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33202

Author: Gladushko Olga

Lab: Lab1

Date of create: 13.12.2022

Date of finished: 16.12.2022

---
# Отчет по лабораторной работе №3
## "Эмуляция распределенной корпоративной сети связи, настройка OSPF и MPLS, организация первого EoMPLS"
Автор: Гладушко Ольга

Группа: К33202

### Цель работы
Изучить протоколы OSPF и MPLS, механизмы организации EoMPLS.

## Ход работы
Текст файла с расширением .yaml прикреплен в папке Lab3. Он был использован для развертывания тестоой сети.

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/Рисунок1.png)

Были настроены IP-адреса на интерфейсах, также был создан виртуальный интерфейс на который был дан IP-адрес. 
Также были настроены OSPF и MPLS на всех роутерах с помощью следующих команд:
```
routing ospf instance set 0 router-id=x.x.x.x
routing ospf network add area=backbone
mpls ldp set enabled=yes transport-address=x.x.x.x
mpls ldp interface add interface=ether*
```
Здесь x.x.x.x - адрес на виртуально интерфейсе, а ether* - интерфейсы смотрящие "внутрь" MPLS сети.

Далее на роутерах NY и SPB была настроена EoMPLS:
```
interface bridge add name=EoMPLS
interface vpls add cisco-style=yes cisco-style-id=666 disabled=no name=EoMPLS_vpls remote-peer=x.x.x.x
interface bridge port add bridge=EoMPLS interface=ether*
interface bridge port add bridge=EoMPLS interface=EoMPLS_vpls
```
Здесь x.x.x.x - также адрес на виртуальном интерфейсе (для NY адрес SPB и наоборот), а ether* - интерфейс, выходящий на SGI_Prism или PC1.

Далее будут представлены конфигурации всех устройств:

### Роутер NY (RO1.NY)
```
/interface bridge
add name=EoMPLS
add name lo0
/interface vpls
add cisco-style=yes cisco-style-id=666 disabled=no l2mtu=1500 mac-address=02:4F:72:7E:70:8C name=EoMPLS_vpls remote-peer=4.4.4.4
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=1.1.1.1
/interface bridge port
add bridge=EoMPLS interface=ether2
add bridge=EoMPLS interface=EoMPLS_vpls
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.1.1/30 interface=ether3
add address=10.0.2.1/30 interface=ether4
add address=1.1.1.1 interface=lo0 network=1.1.1.1
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=1.1.1.1
/mpls ldp interface
add interface=ether3
add interface=ether4
/routing ospf network
add area=backbone
/system identity 
set name=RO1.NY
```

### Роутер LND (RO1.LND)
```
/interface bridge
add name lo0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=2.2.2.2
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=2.2.2.2 interface=lo0 network=2.2.2.2
add address=10.0.1.2/30 interface=ether2
add address=10.0.3.1/30 interface=ether3
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=2.2.2.2
/mpls ldp interface
add interface=ether2
add interface=ether3
/routing ospf network
add area=backbone
/system identity 
set name=RO1.LND
```

### Роутер HKI (RO1.HKI)
```
/interface bridge
add name lo0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=3.3.3.3
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=3.3.3.3 interface=lo0 network=3.3.3.3
add address=10.0.3.2/30 interface=ether2
add address=10.0.4.1/30 interface=ether3
add address=10.0.5.1/30 interface=ether4
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=3.3.3.3
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing ospf network
add area=backbone
/system identity 
set name=RO1.HKI
```

### Роутер SPB (RO1.SPB)
```
/interface bridge
add name=EoMPLS
add name lo0
/interface vpls
add cisco-style=yes cisco-style-id=666 disabled=no l2mtu=1500 mac-address=02:3C:E3:91:56:29 name=EoMPLS_vpls remote-peer=1.1.1.1
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=4.4.4.4
/interface bridge port
add bridge=EoMPLS interface=ether4
add bridge=EoMPLS interface=EoMPLS_vpls
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=4.4.4.4 interface=lo0 network=4.4.4.4
add address=10.0.5.2/30 interface=ether2
add address=10.0.6.1/30 interface=ether3
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=4.4.4.4
/mpls ldp interface
add interface=ether2
add interface=ether3
/routing ospf network
add area=backbone
/system identity 
set name=RO1.SPB
```

### Роутер MSK (RO1.MSK)
```
/interface bridge
add name lo0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=5.5.5.5
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=5.5.5.5 interface=lo0 network=5.5.5.5
add address=10.0.6.2/30 interface=ether3
add address=10.0.7.1/30 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=5.5.5.5
/mpls ldp interface
add interface=ether2
add interface=ether3
/routing ospf network
add area=backbone
/system identity 
set name=RO1.MSK
```

### Роутер LBN (RO1.LBN)
```
/interface bridge
add name lo0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing ospf instance
set [ find default=yes ] router-id=6.6.6.6
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=6.6.6.6 interface=lo0 network=6.6.6.6
add address=10.0.2.2/30 interface=ether2
add address=10.0.4.2/30 interface=ether3
add address=10.0.7.2/30 interface=ether4
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes transport-address=6.6.6.6
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing ospf network
add area=backbone
/system identity 
set name=RO1.LBN
```

На устройствах SGI_Prism и PC1 были выданы IP-адреса на интерфейсы. Их конфигурация:

### SGI_Prism 
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=192.168.10.1/24 interface=ether2 network=192.168.10.0
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=SGI_Prism
```

### PC1 
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=192.168.10.2/24 interface=ether2 network=192.168.10.0
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=PC1
```

Выкладки с маршрутами:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/NY.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/LND.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/HKI.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/SPB.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/MSK.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/LBN.png)

А также проверим сеть на работоспособность пингами с SGI_Prism на PC1 и обратно:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/ping1.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/ping2.png)

### Вывод
В ходе лабораторной работы была сделана IP/MPLS сеть связи, в которой были настроены OSPF И MPLS, а также EoMPLS. Работоспособность сети подтвердили пинги.
