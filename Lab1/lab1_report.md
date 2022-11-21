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
![.](https://drive.google.com/file/d/1szeF23OkE-XU1DgCD5ukEPkXLreQszdt/view?usp=share_link)
Далее были настроены IP-дреса на интерфейсах и 2 VLAN-а для PC1 и PC2. После чего были созданы 2 DHCP-сервера на центральном роутере во VLAN-ах для раздачи IP-адресов. 
Конфигурация центрального роутера (RO1) после проделанных действий:
![.](https://drive.google.com/file/d/1RPyTOLKc0MzEmgpZwWuzpQU87J9dgzlY/view?usp=share_link)
Конфигурация коммутатора 1-го уровня:
![.](https://drive.google.com/file/d/1KCZ7AFmojG7U_-GScgHd65BnkC4wlMdd/view?usp=share_link)
Конфигурация одного из коммутаторов 2-го уровня:
Далее настроим имена устройств: сменим их логины и пароли. Пример (центральный роутер):
![.](https://drive.google.com/file/d/1xBzQMX2Wjvq7dxyuv-Fk5ly5MgfP-SAM/view?usp=share_link)
Проверим, что все работает:
![.](https://drive.google.com/file/d/1cnkFCygiCTNCNT0M-ffPZNDVOFpUIe3P/view?usp=share_link)
![.](https://drive.google.com/file/d/16_mMA4aN0HT1l8RyBXCbgZrHDglU9D8l/view?usp=share_link)

### Вывод
В ходе лабораторной работы была сделана трехуровневая сеть связи в ContainerLab. Сеть состояла из центрального роутера, трех коммутаторов (первого и второго уровней) и двух PC. Были преобретены навыки настройки сетеврого оборудования: настройка IP-адресов и VLAN-ов, создание DHCP-серверов и настройка имен устройств. При этом работоспособность сети подтвердили пинги устройств.