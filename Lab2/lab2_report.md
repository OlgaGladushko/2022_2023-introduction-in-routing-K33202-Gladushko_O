University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33202

Author: Gladushko Olga

Lab: Lab1

Date of create: 18.11.2022

Date of finished: 2.12.2022

---
# Отчет по лабораторной работе №2
## "Эмуляция распределенной корпоративной сети связи, настройка статической маршрутизации между филиалами"
Автор: Гладушко Ольга
Группа: К33202

### Цель работы
Ознакомиться с принципами планирования IP адресов, настройке статической маршрутизации и сетевыми функциями устройств.

## Ход работы
Текст файла с расширением .yaml прикреплен в папке Lab1. Он был использован для развертывания тестоой сети.

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок2.png)

Далее были настроены IP-дреса на интерфейсах роутеров. Также были созданы и включены DHCP-серверы на роутерах с помощью следующих команд:

```
ip pool add name=poolx ranges=192.168.x.5-192.168.x.250
ip dhcp-server nework add address=192.168.x.0/24 gateway=192.168.x.1
ip dhcp-server add address-pool=poolx disabled=no interface=ether4 name=serverx
```
Здесь x - 10, 20 или 30, в зависимости от настраевоемого маршрутизатора (по порядку настройки).

А на PC былы получены адреса с помощью команды:

``` 
ip dhcp-client add disabled=no interface=ether2
```

Далее была настроена статистическая маршрутизация для доступности хостов из других подсетей (для хостов были также добавлены маршруты к другим роутерам). 

Конфигурация роутера MSK (RO1.MSK) после проделанных действий:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок3.png)

Конфигурация роутера FRT (RO1.FRT) после проделанных действий:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок4.png)

Конфигурация роутера BRL (RO1.BRL) после проделанных действий:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок5.png)

Конфигурация ПК MSK (PC1) после проделанных действий:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок6.png)

Конфигурация ПК FRT (PC2) после проделанных действий:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок7.png)

Конфигурация ПК BRL (PC3) после проделанных действий:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок8.png)

Далее настроим имена устройств: сменим их логины и пароли. Пример (роутер MSK):

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок12.png)

Проверим пингами, что все работает:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок9.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок10.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab2/Рисунок11.png)

### Вывод
В ходе лабораторной работы была сделана сеть связи, в которой была настроена статистическая маршрутизация. Также на трех роутерах были созданы DHCP-серверы в сторону клиентских устройств. Работоспособность сети подтвердили пинги.
