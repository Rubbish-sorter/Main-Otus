# Лабораторная работа - Реализация DHCPv4 

## Топология
![lab 8](https://user-images.githubusercontent.com/80044182/122434946-3bcdeb80-cfa0-11eb-81b0-bd71abf165b7.png)
## Таблица адресации
|Устройство |	Интерфейс	|IP-адрес	|Маска подсети	|Шлюз по умолчанию  | 
|--|---|------|-----|----|
|R1	|G0/0/0	|10.0.0.1	|255.255.255.252	|— |  
|R1	|G0/0/1	|—	|—	|— |  
|R1	|G0/0/1.100	|192.168.1.1	|255.255.255.192	|—|
|R1	|G0/0/1.200	|192.168.1.65	|255.255.255.224 | —|
|R1|	G0/0/1.1000|	—|	—	| —|
|R2	|G0/0|	10.0.0.2|	255.255.255.252|	—|
|R2	|G0/0/1|	192.168.1.97|	255.255.255.240	|—|
|S1	|VLAN 200|	192.168.1.67|	255.255.255.224|	192.168.1.65|
|S2	|VLAN 1|	192.168.1.66|	255.255.255.224|	192.168.1.65|
|PC-A	|NIC	|DHCP |	DHCP	| DHCP|
|PC-B	|NIC	|DHCP	|DHCP	|DHCP |

 Возможная ошибка: для S1 получаеся адрес 192.168.1.2/26 не из той сети, заменил на 192.168.1.67/27. 
## Таблица VLAN
|VLAN	| Имя	| Назначенный интерфейс|
|---|---|---|
|1	|Нет	|S2: F0/18|
|100	|Клиенты	|S1: F0/6 |
|200	|Управление	|S1: VLAN 200 |
|999	|Parking_Lot	|S1: F0/1-4, F0/7-24, G0/1-2|
|1000	| Native|	—|

## Задачи
### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
### Часть 3. Настройка и проверка DHCP-ретрансляции на R2
## Необходимые ресурсы
-	2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
-	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
-	2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
-	Консольные кабели для настройки устройств Cisco IOS через консольные порты.
-	Кабели Ethernet, расположенные в соответствии с топологией
## Инструкции
### Часть 1. Создание сети и настройка основных параметров устройства
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.
#### Шаг 1. Создание схемы адресации
Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями: 
a.	Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1). 
Подсеть A 
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию. 
b.	Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1).  
Подсеть B:  
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 1 и введите соответствующий шлюз по умолчанию.    
c.	Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).  
Подсеть C:  
Запишите первый IP-адрес в таблице адресации для R2 G0 /0/1.  
#### Шаг 2. Создайте сеть согласно топологии.  
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.    
#### Шаг 3. Произведите базовую настройку маршрутизаторов.  
a.	Назначьте маршрутизатору имя устройства.  
Откройте окно конфигурации  
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.  
f.	Зашифруйте открытые пароли.  
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
i.	Установите часы на маршрутизаторе на сегодняшнее время и дату.  
**R1**
Router>en  
Router# clock set 20:00:00 10 Jun 2021  
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1  
R1(config)#no ip domain-lookup  
R1(config)#ser pass  
R1(config)#banner motd #Unauthorized access is strictly prohibited!#  
R1(config)#enable secret class    
R1(config)#line con 0  
R1(config-line)#pass cisco  
R1(config-line)#login  
R1(config-line)#logging-sync  
R1(config-line)#line vty 0 4  
R1(config-line)#pass cisco   
R1(config-line)#login   
R1(config-line)#exit
**R2**
Router>en  
Router# clock set 20:03:00 10 Jun 2021  
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.    
Router(config)#hostname R2    
R2(config)#no ip domain-lookup    
R2(config)#ser pass  
R2(config)#banner motd #Unauthorized access is strictly prohibited!#    
R2(config)#enable secret class     
R2(config)#line con 0   
R2(config-line)#pass cisco  
R2(config-line)#login   
R2(config-line)#logging-sync   
R2(config-line)#line vty 0 4   
R2(config-line)#pass cisco    
R2(config-line)#login    
R2(config-line)#exit   
#### Шаг 4. Настройка маршрутизации между сетями VLAN на маршрутизаторе R1
a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.    
b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.  
c.	Убедитесь, что вспомогательные интерфейсы работают.  
R1(config-if)#int g0/0/1  
R1(config-if)#no sh  
R1(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up  
R1(config-if)#int g0/0/1.100  
R1(config-subif)#enc dot1q 100  
R1(config-subif)#ip addr 192.168.1.1 255.255.255.192  
R1(config-subif)#int g0/0/1.200  
R1(config-subif)#enc dot1q 200  
R1(config-subif)#ip addr 192.168.1.65 255.255.255.224  
R1(config-subif)#int g0/0/1.1000  
R1(config-subif)#enc dot1q 1000 native  
R1(config-subif)#exit  
R1(config)#do ping 192.168.1.65  

Type escape sequence to abort.  
Sending 5, 100-byte ICMP Echos to 192.168.1.65, timeout is 2 seconds:  
!!!!!  
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/5/9 ms  

R1(config)#do ping 192.168.1.1    

Type escape sequence to abort.    
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:    
!!!!!    
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/7/16 ms   
#### Шаг 5. Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов  
a.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.     
b.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.    
c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.  
d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.  
e.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
**R1**
R1(config)#int g0/0/0
R1(config-if)#ip addr 10.0.0.1 255.255.255.252   
R1(config-if)#no sh  
R1(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up  
R1(config-if)#exit  
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2  
R1(config)#copy run start  
Building configuration...  
[OK]  
**R2**   
R2(config)#int g0/0/0  
R2(config-if)#ip addr 10.0.0.2 255.255.255.252  
R2(config-if)#no sh  
R2(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up  

R2(config-if)#int g0/0/1  
R2(config-if)#ip addr 192.168.1.97 255.255.255.240  
R2(config-if)#no sh    
R2(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up  

R2(config-if)#exit 
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1  
R2(config)#do copy run start  
Destination filename [startup-config]?   
Building configuration...  
[OK]  
#### Шаг 6. Настройте базовые параметры каждого коммутатора.  
a.	Присвойте коммутатору имя устройства.  
Откройте окно конфигурации  
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.  
f.	Зашифруйте открытые пароли.  
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
i.	Установите часы на сегодняшнее время и дату.  
j.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации    
**S1**  
Switch>en  
Switch#clock set 20:10:00 10 Jun 2021   
Switch#conf t    
Enter configuration commands, one per line. End with CNTL/Z.    
Switch(config)#hostname S1    
S1(config)#no ip domain-lookup   
S1(config)#banner motd #Unaithorized accesss is strictly prohibited!#  
S1(config)#ser pass    
S1(config)#enable secret class   
S1(config)#line con 0   
S1(config-line)#logging sync   
S1(config-line)#pass cisco     
S1(config-line)#login  
S1(config-line)#line vty 0 4  
S1(config-line)#pass cisco    
S1(config-line)#login    
S1(config-line)#exit   
S1(config)#do copy run start  
Destination filename [startup-config]?   
Building configuration...  
[OK]  
**S2**  
Switch>en  
Switch#clock set 20:20:00 10 Jun 2021   
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S1  
S2(config)#no ip domain-lookup  
S2(config)#banner motd #Unaithorized accesss is strictly prohibited!#  
S2(config)#ser pass  
S2(config)#enable secret class  
S2(config)#line con 0  
S2(config-line)#logging sync  
S2(config-line)#pass cisco  
S2(config-line)#login  
S2(config-line)#line vty 0 4  
S2(config-line)#pass cisco  
S2(config-line)#login  
S2(config-line)#exit  
S2(config)#do copy run start  
Destination filename [startup-config]?   
Building configuration...  
[OK] 
Шаг 7. Создайте сети VLAN на коммутаторе S1.
Примечание. S2 настроен только с базовыми настройками. 
a.	Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2
d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.


