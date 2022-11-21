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
# Отчет по лабораторной работе №1
## "Установка ContainerLab и развертывание тестовой сети связи"
Автор: Гладушко Ольга
Группа: К33202

### Цель работы
Ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т.д.

## Ход работы
Текст файла с расширением .yaml прикреплен в папке Lab1. Он был использован для развертывания тестоой сети.
![.](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок1.png)
Далее были настроены IP-дреса на интерфейсах и 2 VLAN-а для PC1 и PC2. После чего были созданы 2 DHCP-сервера на центральном роутере во VLAN-ах для раздачи IP-адресов. 
Конфигурация центрального роутера (RO1) после проделанных действий:
![.](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок2.png)
Конфигурация коммутатора 1-го уровня:
![.](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок3.png)
Конфигурация одного из коммутаторов 2-го уровня:
![](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок4.png)
Далее настроим имена устройств: сменим их логины и пароли. Пример (центральный роутер):
![.](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок6.png)
Проверим, что все работает:
![.](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок5.png)
![.](https://github.com/OlgaGladushko/pictures/blob/main/Рисунок8.png)

### Вывод
В ходе лабораторной работы была сделана трехуровневая сеть связи в ContainerLab. Сеть состояла из центрального роутера, трех коммутаторов (первого и второго уровней) и двух PC. Были преобретены навыки настройки сетеврого оборудования: настройка IP-адресов и VLAN-ов, создание DHCP-серверов и настройка имен устройств. При этом работоспособность сети подтвердили пинги устройств.
