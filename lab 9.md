# Лабораторная работа - Конфигурация безопасности коммутатора 

##Топология
![lab 9](https://user-images.githubusercontent.com/80044182/123926562-a9145000-d994-11eb-8552-b851c80f7fe4.png)

##Таблица адресации
|Устройство	|interface |IP-адрес	|Маска подсети |
|--|------|---|----|
|R1	|G0/0/1	|192.168.10.1	|255.255.255.0|
|R1	|Loopback 0 |	10.10.1.1	|255.255.255.0|
|S1	|VLAN 10	|192.168.10.201 |	255.255.255.0|
|S2	|VLAN 10	|192.168.10.202	|255.255.255.0|
|PC A	|NIC	|DHCP	|255.255.255.0|
|PC B	|NIC	|DHCP	|255.255.255.0|

## Цели 

### Часть 1. Настройка основного сетевого устройства 
-	Создайте сеть.
-	Настройте маршрутизатор R1.
-	Настройка и проверка основных параметров коммутатора
### Часть 2. Настройка сетей VLAN
-	Сконфигруриуйте VLAN 10.
-	Сконфигруриуйте SVI для VLAN 10.
-	Настройте VLAN 333 с именем Native на S1 и S2.
-	Настройте VLAN 999 с именем ParkingLot на S1 и S2.
### Часть 3: Настройки безопасности коммутатора.
-	Реализация магистральных соединений 802.1Q.
-	Настройка портов доступа
-	Безопасность неиспользуемых портов коммутатора
-	Документирование и реализация функций безопасности порта.
-	Реализовать безопасность DHCP snooping .
-	Реализация PortFast и BPDU Guard
-	Проверка сквозной связанности.

## Необходимые ресурсы
-	1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.3 или аналогичным)
-	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель)
-	2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
-	Консольные кабели для настройки устройств Cisco IOS через консольные порты.
-	Кабели Ethernet, расположенные в соответствии с топологией
## Инструкции
### Часть 1. Настройка основного сетевого устройства
#### Шаг 1. Создайте сеть.
a.	Создайте сеть согласно топологии.  
b.	Инициализация устройств  
#### Шаг 2. Настройте маршрутизатор R1.    
a.	Загрузите следующий конфигурационный скрипт на R1.  
Откройте окно конфигурации  
enable  
configure terminal  
hostname R1  
no ip domain lookup  
ip dhcp excluded-address 192.168.10.1 192.168.10.9  
ip dhcp excluded-address 192.168.10.201 192.168.10.202  
!  
ip dhcp pool Students  
 network 192.168.10.0 255.255.255.0  
 default-router 192.168.10.1  
 domain-name CCNA2.Lab-11.6.1  
!  
interface Loopback0  
 ip address 10.10.1.1 255.255.255.0  
!  
interface GigabitEthernet0/0/1  
 description Link to S1  
 ip dhcp relay information trusted  
 ip address 192.168.10.1 255.255.255.0  
 no shutdown  
!  
line con 0  
 logging synchronous  
 exec-timeout 0 0  
 R1(config-line)#  
%LINK-5-CHANGED: Interface Loopback0, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback0, changed state to up  

%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up  

R1(config-line)#exit  
b.	Проверьте текущую конфигурацию на R1, используя следующую команду:    
R1# show ip interface brief    
c.	Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).    
R1(config)#do sh ip int br  
Interface IP-Address OK? Method Status Protocol   
GigabitEthernet0/0/0 unassigned YES unset administratively down down    
GigabitEthernet0/0/1 192.168.10.1 YES manual up up   
Loopback0 10.10.1.1 YES manual up up   
Vlan1 unassigned YES unset administratively down down

#### Шаг 3. Настройка и проверка основных параметров коммутатора    
a.	Настройте имя хоста для коммутаторов S1 и S2.      
Откройте окно конфигурации    
b.	Запретите нежелательный поиск в DNS.    
c.	Настройте описания интерфейса для портов, которые используются в S1 и S2.    
d.	Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.    
**S1**  
Switch>en  
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S1  
S1(config)#no ip domain lookup  
S1(config)#line con 0  
S1(config-line)#logging sync  
S1(config-line)#exit  
**S2**  
Switch>en  
Switch#conf t   
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S2  
S2(config)#no ip domain lookup  
S2(config)#line con 0    
S2(config-line)#logging sync    
S2(config-line)#exit  
Настройка основно шлюза для VLAN управления и описания используемых интерфейсов везде перенесены далее.  
### Часть 2. Настройка сетей VLAN на коммутаторах.
#### Шаг 1. Сконфигруриуйте VLAN 10.
Добавьте VLAN 10 на S1 и S2 и назовите VLAN - Management.
#### Шаг 2. Сконфигруриуйте SVI для VLAN 10.
#### Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.
#### Шаг 3. Настройте VLAN 333 с именем Native на S1 и S2.
#### Шаг 4. Настройте VLAN 999 с именем ParkingLot на S1 и S2.
**S1**  
S1(config)#vlan 10   
S1(config-vlan)#name Management   
S1(config-vlan)#vlan 333  
S1(config-vlan)#name Native   
S1(config-vlan)#vlan 999  
S1(config-vlan)#name Parking_lot      
S1(config-vlan)#int int vlan 10    
S1(config-if)#    
%LINK-5-CHANGED: Interface Vlan10, changed state to up    

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up  

S1(config-if)#ip addr 192.168.10.201   
S1(config-if)#ip addr 192.168.10.201 255.255.255.0  
S1(config-if)#ip def 192.168.10.1    
S1(config-if)#exit  
**S2**
S2(config)#vlan 10   
S2(config-vlan)#name Management   
S2(config-vlan)#vlan 333  
S2(config-vlan)#name Native   
S2(config-vlan)#vlan 999  
S2(config-vlan)#name Parking_lot  
S2(config-vlan)#int int vlan 10  
S2(config-if)#  
%LINK-5-CHANGED: Interface Vlan10, changed state to up    

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up  

S2(config-if)#ip addr 192.168.10.201   
S2(config-if)#ip addr 192.168.10.201 255.255.255.0 
S2(config-if)#ip def 192.168.10.1
S2(config-if)#exit  
### Часть 3. Настройки безопасности коммутатора.
#### Шаг 1. Релизация магистральных соединений 802.1Q.
a.	Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.  
b.	Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.  
**S1**
S1(config)#int f0/1  
S1(config-if)#sw mode tr  
S1(config-if)#sw tr native vlan 333  
S1(config-if)#exit  
S1(config)#do sh int tr  
Port Mode Encapsulation Status Native vlan 
Fa0/1 on 802.1q trunking 333  

Port Vlans allowed on trunk  
Fa0/1 1-1005  

Port Vlans allowed and active in management domain  
Fa0/1 1,10,333,999  

Port Vlans in spanning tree forwarding state and not pruned  
Fa0/1 1,10,333,999  
**S2**
S2(config)#int f0/1  
S2(config-if)#sw mode tr  
S2(config-if)#sw tr native vlan 333  
S2(config-if)#exit  
S2(config)#do sh int tr  
Port Mode Encapsulation Status Native vlan 
Fa0/1 on 802.1q trunking 333  

Port Vlans allowed on trunk  
Fa0/1 1-1005  

Port Vlans allowed and active in management domain  
Fa0/1 1,10,333,999  

Port Vlans in spanning tree forwarding state and not pruned  
Fa0/1 1,10,333,999  
Шаг 2. Настройка портов доступа
a.	На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.
b.	На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.

Шаг 3. Безопасность неиспользуемых портов коммутатора
a.	На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.
b.	Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999, введя команду  show.
S1(config)#int range f0/1-4  
S1(config-if-range)#sw mode access   
S1(config-if-range)#sw acc vlan 999  
S1(config-if-range)#sh    
  
%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down    

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down    

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down    

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down  
S1(config-if-range)#int range f0/7-24    
S1(config-if-range)#sw mode access     
S1(config-if-range)#sw acc vlan 999    
S1(config-if-range)#sh    

%LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down  

- - - - - -   

S1(config-if-range)#int range g0/1-2   
S1(config-if-range)#sw mode access   
S1(config-if-range)#sw acc vlan 999  
S1(config-if-range)#sh  

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down  

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down  
S1(config-if-range)#exit  
