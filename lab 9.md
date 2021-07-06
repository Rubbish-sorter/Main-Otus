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
**S1**  
S1(config)#int f0/5    
S1(config-if)#sw mode access   
S1(config-if)#sw acc vlan 10 
S1(config-if)#description to R1  
S1(config-if)#int f0/6   
S1(config-if)#sw mode access    
S1(config-if)#sw acc vlan 10   
S1(config-if)#description to PC-A  
**S2**
S1(config)#int f0/18  
S1(config-if)#sw mode access   
S1(config-if)#sw acc vlan 10 
S1(config-if)#description to PC-B
Шаг 3. Безопасность неиспользуемых портов коммутатора  
a.	На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.      
b.	Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999, введя команду  show.    
**S1**    
S1(config)#int range f0/2-4    
S1(config-if-range)#sw mode access     
S1(config-if-range)#sw acc vlan 999  
S1(config-if-range)#sh    
  
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
S1(config)#do sh int status
Port Name Status Vlan Duplex Speed Type
Fa0/1 connected 1 auto auto 10/100BaseTX  
Fa0/2 disabled 999 auto auto 10/100BaseTX  
Fa0/3 disabled 999 auto auto 10/100BaseTX  
Fa0/4 disabled 999 auto auto 10/100BaseTX  
Fa0/5 connected 10 auto auto 10/100BaseTX  
Fa0/6 connected 10 auto auto 10/100BaseTX  
Fa0/7 disabled 999 auto auto 10/100BaseTX  
Fa0/8 disabled 999 auto auto 10/100BaseTX  
Fa0/9 disabled 999 auto auto 10/100BaseTX  
Fa0/10 disabled 999 auto auto 10/100BaseTX  
Fa0/11 disabled 999 auto auto 10/100BaseTX  
Fa0/12 disabled 999 auto auto 10/100BaseTX  
Fa0/13 disabled 999 auto auto 10/100BaseTX  
Fa0/14 disabled 999 auto auto 10/100BaseTX  
Fa0/15 disabled 999 auto auto 10/100BaseTX  
Fa0/16 disabled 999 auto auto 10/100BaseTX  
Fa0/17 disabled 999 auto auto 10/100BaseTX  
Fa0/18 disabled 999 auto auto 10/100BaseTX
Fa0/19 disabled 999 auto auto 10/100BaseTX    
Fa0/20 disabled 999 auto auto 10/100BaseTX  
Fa0/21 disabled 999 auto auto 10/100BaseTX  
Fa0/22 disabled 999 auto auto 10/100BaseTX  
Fa0/23 disabled 999 auto auto 10/100BaseTX  
Fa0/24 disabled 999 auto auto 10/100BaseTX  
Gig0/1 disabled 999 auto auto 10/100BaseTX  
Gig0/2 disabled 999 auto auto 10/100BaseTX  
**S2**    
S2(config)#int range f0/2-17    
S2(config-if-range)#sw mode access     
S2(config-if-range)#sw acc vlan 999  
S2(config-if-range)#sh    
  
%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down  

--- --- --- --- 
S2(config-if-range)#int range f0/19-24    
S2(config-if-range)#sw mode access     
S2(config-if-range)#sw acc vlan 999    
S2(config-if-range)#sh    

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down  

- - - - - -   

S2(config-if-range)#int range g0/1-2   
S2(config-if-range)#sw mode access   
S2(config-if-range)#sw acc vlan 999  
S2(config-if-range)#sh  

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down  

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down  
S2(config-if-range)#exit  
S2(config)#do sh int status  
Port Name Status Vlan Duplex Speed Type  
Fa0/1 connected 1 auto auto 10/100BaseTX  
Fa0/2 disabled 999 auto auto 10/100BaseTX  
Fa0/3 disabled 999 auto auto 10/100BaseTX  
Fa0/4 disabled 999 auto auto 10/100BaseTX  
Fa0/5 disabled 999 auto auto 10/100BaseTX  
Fa0/6 disabled 999 auto auto 10/100BaseTX  
Fa0/7 disabled 999 auto auto 10/100BaseTX  
Fa0/8 disabled 999 auto auto 10/100BaseTX  
Fa0/9 disabled 999 auto auto 10/100BaseTX  
Fa0/10 disabled 999 auto auto 10/100BaseTX  
Fa0/11 disabled 999 auto auto 10/100BaseTX  
Fa0/12 disabled 999 auto auto 10/100BaseTX  
Fa0/13 disabled 999 auto auto 10/100BaseTX  
Fa0/14 disabled 999 auto auto 10/100BaseTX  
Fa0/15 disabled 999 auto auto 10/100BaseTX  
Fa0/16 disabled 999 auto auto 10/100BaseTX  
Fa0/17 disabled 999 auto auto 10/100BaseTX  
Fa0/18 connected 10 auto auto 10/100BaseTX  
Fa0/19 disabled 999 auto auto 10/100BaseTX  
Fa0/20 disabled 999 auto auto 10/100BaseTX  
Fa0/21 disabled 999 auto auto 10/100BaseTX  
Fa0/22 disabled 999 auto auto 10/100BaseTX  
Fa0/23 disabled 999 auto auto 10/100BaseTX  
Fa0/24 disabled 999 auto auto 10/100BaseTX  
Gig0/1 disabled 999 auto auto 10/100BaseTX  
Gig0/2 disabled 999 auto auto 10/100BaseTX  
#### Шаг 4. Документирование и реализация функций безопасности порта.  
Интерфейсы F0/6 на S1 и F0/18 на S2 настроены как порты доступа. На этом шаге вы также настроите безопасность портов на этих двух портах доступа.  
a.	На S1, введите команду show port-security interface f0/6  для отображения настроек по умолчанию безопасности порта для интерфейса F0/6.  
Запишите свои ответы ниже.    
**Конфигурация безопасности порта по умолчанию**  
|Функция	|Настройка по умолчанию|
|-----|----| 
|Защита портов	| Отключено|  
|Максимальное количество записей MAC-адресов|	1|  
|Режим проверки на нарушение безопасности	| отключено|  
|Aging Time	| 0|  
|Aging Type	| absolute|  
|Secure Static Address Aging	| disabled|  
|Sticky MAC Address	| 0|  

S1(config)#do show port-security interface f0/6  
Port Security : Disabled  
Port Status : Secure-down  
Violation Mode : Shutdown  
Aging Time : 0 mins  
Aging Type : Absolute  
SecureStatic Address Aging : Disabled  
Maximum MAC Addresses : 1  
Total MAC Addresses : 0  
Configured MAC Addresses : 0  
Sticky MAC Addresses : 0  
Last Source Address:Vlan : 0000.0000.0000:0  
Security Violation Count : 0  
b.	На S1 включите защиту порта на F0 / 6 со следующими настройками:  
o	Максимальное количество записей MAC-адресов: 3  
o	Режим безопасности: restrict  
o	Aging time: 60 мин.  
o	Aging type: неактивный  
c.	Verify port security on S1 F0/6.  
S1(config)#int f0/6  
S1(config-if)#sw port-security   
S1(config-if)#sw port-security violation restrict  
S1(config-if)#sw port-security maximum 3  
S1(config-if)#switchport port-security aging time 60  
S1(config-if)#sw port-sec aging type inactity  
S1(config)#do sh port-sec int f0/6  
Port Security : Enabled  
Port Status : Secure-up  
Violation Mode : Restrict  
Aging Time : 60 mins  
Aging Type : Absolute  
SecureStatic Address Aging : Disabled  
Maximum MAC Addresses : 3  
Total MAC Addresses : 0  
Configured MAC Addresses : 0  
Sticky MAC Addresses : 0  
Last Source Address:Vlan : 0000.0000.0000:0  
Security Violation Count : 0 

S1(config)#do sh port-sec addr  
Secure Mac Address Table  
-------------------------------------------------------------------------------  
Vlan Mac Address Type Ports Remaining Age   
(mins)  
---- ----------- ---- ----- -------------  
10 0060.2F7D.3E09 DynamicConfigured FastEthernet0/6 -  
------------------------------------------------------------------------------   
Total Addresses in System (excluding one mac per port) : 0    
Max Addresses limit in System (excluding one mac per port) : 1024  
d.	Включите безопасность порта для F0 / 18 на S2. Настройте каждый активный порт доступа таким образом, чтобы он автоматически добавлял адреса МАС,   изученные на этом порту, в текущую конфигурацию.  
e.	Настройте следующие параметры безопасности порта на S2 F / 18:  
-	Максимальное количество записей MAC-адресов: 2  
-	Тип безопасности: Protect    
-	Aging time: 60 мин.  
S2(config)#int f0/18  
S2(config-if)#sw port-sec  
S2(config-if)#sw port-sec viol protect  
S2(config-if)#sw port-sec ag time 60  
S2(config-if)#exit  
f.	Проверка функции безопасности портов на S2 F0/18.    
S2(config)#do sh port-sec int f0/18  
Port Security              : Enabled  
Port Status                : Secure-up  
Violation Mode             : Protect  
Aging Time                 : 60 mins  
Aging Type                 : Absolute  
SecureStatic Address Aging : Disabled  
Maximum MAC Addresses      : 1  
Total MAC Addresses        : 1  
Configured MAC Addresses   : 0  
Sticky MAC Addresses       : 1  
Last Source Address:Vlan   : 000A.F3A6.0D11:10  
Security Violation Count   : 0  

S2(config)#do sh port-sec addr  
			Secure Mac Address Table  
-------------------------------------------------------------------------------  
Vlan	Mac Address	Type			Ports		Remaining Age  
								(mins)
----	-----------	----			-----		-------------  
10	000A.F3A6.0D11	SecureSticky		FastEthernet0/18		-  
------------------------------------------------------------------------------  
Total Addresses in System (excluding one mac per port)     : 0  
Max Addresses limit in System (excluding one mac per port) : 1024  
#### Шаг 5. Реализовать безопасность DHCP snooping.  
a.	На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.  
b.	Настройте магистральные порты на S2 как доверенные порты.  
c.	Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.  
d.	Проверка DHCP Snooping на S2.  
S2(config)#ip dhcp sn  
S2(config)#ip dhcp snooping vlan 10  
S2(config)#no ip dhcp sn inf opt  
S2(config)#int f0/1  
S2(config-if)#ip dhcp sn trust  
S2(config-if)#int f0/18  
S2(config-if)#ex  
S2(config)#int f0/18  
S2(config-if)#ip dhcp snooping limit rate 5  
S2(config-if)#exit  

S2(config)#do sh ip dhcp sn  
Switch DHCP snooping is enabled  
DHCP snooping is configured on following VLANs: 1, 10  
Insertion of option 82 is disabled  
Option 82 on untrusted port is not allowed   
Verification of hwaddr field is enabled  
Interface Trusted Rate limit (pps)  
----------------------- ------- ----------------  
FastEthernet0/1 yes unlimited   
FastEthernet0/18 no 5  

e.	В командной строке на PC-B освободите, а затем обновите IP-адрес.     
C:\>ipconfig /release  

IP Address......................: 0.0.0.0  
Subnet Mask.....................: 0.0.0.0  
Default Gateway.................: 0.0.0.0    
DNS Server......................: 0.0.0.0   

C:\>ipconfig /renew  

IP Address......................: 192.168.10.11  
Subnet Mask.....................: 255.255.255.0  
Default Gateway.................: 192.168.10.1  
DNS Server......................: 0.0.0.0   

f.	Проверьте привязку отслеживания DHCP с помощью команды show ip dhcp snooping binding.  
S2(config)#do sh ip dhcp sn bind    
MacAddress IpAddress Lease(sec) Type VLAN Interface  
------------------ --------------- ---------- ------------- ---- -----------------  
00:0A:F3:A6:0D:11 192.168.10.11 86400 dhcp-snooping 10 FastEthernet0/18  
Total number of bindings: 1  

#### Шаг 6. Реализация PortFast и BPDU Guard    
a.	Настройте PortFast на всех портах доступа, которые используются на обоих коммутаторах.    
b.	Включите защиту BPDU на портах доступа VLAN 10 S1 и S2, подключенных к PC-A и PC-B.   
c.	Убедитесь, что защита BPDU и PortFast включены на соответствующих портах.    
**S1**
S1(config)#int f0/5    
S1(config-if)#span portfast    
%Warning: portfast should only be enabled on ports connected to a single  
host. Connecting hubs, concentrators, switches, bridges, etc... to this  
interface when portfast is enabled, can cause temporary bridging loops.  
Use with CAUTION   

%Portfast has been configured on FastEthernet0/5 but will only  
have effect when the interface is in a non-trunking mode.  

S1(config-if)#spanning-tree bpduguard enable  
S1(config-if)#int f0/6   
S1(config-if)#span portfast  
S1(config-if)#spanning-tree bpduguard enable  
S1(config-if)#exit  
S1(config)#do sh span int f0/6 det   
Port 6 (FastEthernet0/6) of VLAN0010 is designated forwarding  
Port path cost 19, Port priority 128, Port Identifier 128.6  
Designated root has priority 32778, address 0001.6374.3480  
Designated bridge has priority 32778, address 0060.2FCD.788B 
Designated port id is 128.6, designated path cost 19  
Timers: message age 16, forward delay 0, hold 0 
Number of transitions to forwarding state: 1  
The port is in the portfast mode  
Link type is point-to-point by default  
**S2**
S2(config)#int f0/18    
S2(config-if)#span portfast    
%Warning: portfast should only be enabled on ports connected to a single  
host. Connecting hubs, concentrators, switches, bridges, etc... to this  
interface when portfast is enabled, can cause temporary bridging loops.  
Use with CAUTION   

%Portfast has been configured on FastEthernet0/5 but will only  
have effect when the interface is in a non-trunking mode.  

S2(config-if)#spanning-tree bpduguard enable 
S2(config-if)#exit
Шаг 7. Проверьте наличие сквозного ⁪подключения.
Проверьте PING свзяь между всеми устройствами в таблице IP-адресации. В случае сбоя проверки связи может потребоваться отключить брандмауэр на хостах.
C:\>ping 192.168.10.1  

Pinging 192.168.10.1 with 32 bytes of data:  

Reply from 192.168.10.1: bytes=32 time=10ms TTL=255  
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255  
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255  
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255  

Ping statistics for 192.168.10.1:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 10ms, Average = 2ms  

C:\>ping 192.168.10.201  

Pinging 192.168.10.201 with 32 bytes of data:  

Request timed out.  
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255  
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255  
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255 

Ping statistics for 192.168.10.201: 
Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms  

C:\>ping 192.168.10.202  

Pinging 192.168.10.202 with 32 bytes of data:  

Request timed out.  
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255  
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255  
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255  

Ping statistics for 192.168.10.202:  
Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms  

C:\>ping 192.168.10.11  

Pinging 192.168.10.11 with 32 bytes of data:  

Reply from 192.168.10.11: bytes=32 time<1ms TTL=128  
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128  
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128  
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128    

Ping statistics for 192.168.10.11:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms  

**Вопросы для повторения:**
1.	С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах, когда было сконфигурировано динамическое обучение - sticky? Такого не было, ошибка.
2.	Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порту 18 на PC-B никогда не получит IP-адрес через DHCP? Некорректный вопрос или не отключена опция 82.
3.	Что касается безопасности порта, в чем разница между типом абсолютного устаревания и типом устаревание по неактивности?
**Ответы:**
1.   Такого не было, ошибка.   
2.  Некорректный вопрос или не отключена опция 82.   
3. Абсолютное устаревание считается только по времени, а устаревание по неактивности считается только если нет кадров через этот порт.
 
 
