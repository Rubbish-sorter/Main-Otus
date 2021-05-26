# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями 
## Топология
![lab 6](https://user-images.githubusercontent.com/80044182/119564041-f9562c00-bdb0-11eb-97e1-ed8bbffc396d.png)

## Таблица адресации
| Устройство	| Интерфейс|	IP-адрес|	Маска подсети|	Шлюз по умолчанию|
|--|-----|-----|------|-----|
|R1	|G0/0/1.10	|192.168.10.1|	255.255.255.0	|—|
|R1	|G0/0/1.20	|192.168.20.1|	255.255.255.0	|—|
|R1|	G0/0/1.30|	192.168.30.1	|255.255.255.0|	—|
|R1	|G0/0/1.1000|	—|	—	|—|
|S1	|VLAN 10|	192.168.10.11|	255.255.255.0|	192.168.10.1|
|S2	|VLAN 10|	192.168.10.12|	255.255.255.0|	192.168.10.1|
|S2	|VLAN 10|	192.168.10.12|	255.255.255.0|	192.168.10.1|
|PC-A	|NIC	|192.168.20.3	|255.255.255.0	|192.168.20.1|
|PC-B|	NIC	|192.168.30.3|	255.255.255.0|	192.168.30.1|
## Таблица VLAN
|VLAN|	Имя|	Назначенный интерфейс|
|---|----|-------|
|10	|Management|	S1: VLAN 10 
  ||              S2: VLAN 10 |
|20	|Sales|	S1: F0/6|
|30	|Operations	|S2: F0/18|
|999|	Parking_Lot	| С1: F0/2-4, F0/7-24, G0/1-2
 ||             |   С2: F0/2-17, F0/19-24, G0/1-2|
| 1000| Native|	—|
## Задачи
**Часть 1. Создание сети и настройка основных параметров устройства  
Часть 2. Создание сетей VLAN и назначение портов коммутатора  
Часть 3. Настройка транка 802.1Q между коммутаторами.  
Часть 4. Настройка маршрутизации между сетями VLAN  
Часть 5. Проверка, что маршрутизация между VLAN работает**

### Часть 1. Создание сети и настройка основных параметров устройства  
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.  
Шаг 1. Создайте сеть согласно топологии.  
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.  
Шаг 2. Настройте базовые параметры для маршрутизатора.  
a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.  
Откройте окно конфигурации  
b.	Войдите в режим конфигурации.  
c.	Назначьте маршрутизатору имя устройства.  
d.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
e.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
f.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
g.	Установите cisco в качестве пароля виртуального терминала и активируйте вход.
h.	Зашифруйте открытые пароли.  
i.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
k.	Настройте на маршрутизаторе время.   

Router>en  
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1   
R1(config)#no ip domain lookup  
R1(config)#enable secret class  
R1(config)#ser pass  
R1(config)#banner motd #Unauthorized access is strictly prohibited!#  
R1(config)#line con 0  
R1(config-line)#logging sync  
R1(config-line)#pass cisco  
R1(config-line)#login  
R1(config-line)#line vty 0 4  
R1(config-line)#pass cisco  
R1(config-line)#login  
R1(config-line)#^Z  
R1#  
%SYS-5-CONFIG_I: Configured from console by console  

R1#clock set 21:00:00 05 may 2021  
R1#wr mem  
Building configuration...  
[OK]   
 ### Шаг 3. Настройте базовые параметры каждого коммутатора.
a.	Присвойте коммутатору имя устройства.  
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
e.	Установите cisco в качестве пароля виртуального терминала и активируйте вход.  
f.	Зашифруйте открытые пароли.  
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
h.	Настройте на коммутаторах время.  
i.	Сохранение текущей конфигурации в качестве начальной.  
**S1**  
Switch>en  
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S1  
S1(config)#banner motd #Unauthorized access is strictly prohibited!#  
S1(config)#no ip domain lookup  
S1(config)#ser pass  
S1(config)#enable secret class   
S1(config)#line con 0  
S1(config-line)#logging sync  
S1(config-line)#pass cisco  
S1(config-line)#login  
S1(config-line)#line vty 0 4  
S1(config-line)#pass cisco  
S1(config-line)#login  
S1(config-line)#^Z  
S1#  
%SYS-5-CONFIG_I: Configured from console by console  

S1#clock set 21:05:00 5 may 2021  
S1#wr mem  
Building configuration...  
[OK]  
**S2**  
Switch>en  
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S2  
S2(config)#banner motd #Unauthorized access is strictly prohibited!#  
S2(config)#ser pass  
S2(config)#enable secret class  
S2(config)#no ip domain lookup  
S2(config)#line con 0  
S2(config-line)#pass cisco  
S2(config-line)#login  
S2(config-line)#line vty 0 4  
S2(config-line)#pass cisco  
S2(config-line)#login  
S2(config-line)#^Z  
S2#  
%SYS-5-CONFIG_I: Configured from console by console  
 
S2#clock set 21:10:00 5 may 2021  
S2#wr mem  
Building configuration...  
[OK]  

#### Шаг 4. Настройте узлы ПК.  
Адреса ПК можно посмотреть в таблице адресации.  
**PC-A**  
C:\>ipconfig  
FastEthernet0 Connection:(default port)  

   Connection-specific DNS Suffix..:    
   Link-local IPv6 Address.........: FE80::20D:BDFF:FEC9:49E7  
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.20.3  
   Subnet Mask.....................: 255.255.255.0  
   Default Gateway.................: ::  
                                     192.168.20.1  
**PC-B**  
C:\>ipconfig  
FastEthernet0 Connection:(default port)  
 
   Connection-specific DNS Suffix..:   
   Link-local IPv6 Address.........: FE80::2D0:BCFF:FEAD:1215  
   IPv6 Address....................: ::  
   IPv4 Address....................: 192.168.30.3  
   Subnet Mask.....................: 255.255.255.0  
   Default Gateway.................: ::  
                                     192.168.30.1  
                                     

### Часть 2. Создание сетей VLAN и назначение портов коммутатора    
Шаг 1. Создайте сети VLAN на коммутаторах.   
a.	Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.  
Откройте окно конфигурации  
b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.   
c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.  
**S1**  
S1>en  
Password:   
S1#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
S1(config)#vlan 10  
S1(config-vlan)#name management  
S1(config)#vlan 20  
S1(config-vlan)#name sales  
S1(config-vlan)#vlan 999  
S1(config-vlan)#name parking_lot   
S1(config-vlan)#exit 
S1(config-if)#int vlan 10    
S1(config-if)#   
%LINK-5-CHANGED: Interface Vlan10, changed state to up   

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up   

S1(config-if)#ip addr 192.168.10.11 255.255.255.0   
S1(config-if)#ip default-gateway 192.168.10.1  
S1(config-if)#int range f0/2-4  
S1(config-if-range)#switchport mode access  
S1(config-if-range)#sw acc vlan 999  
S1(config-if-range)#sh   
S1(config-if-range)#exit  
S1(config)#int range f0/7-24  
S1(config-if-range)#sw mod acc  
S1(config-if-range)#sw acc vlan 999  
S1(config-if-range)#sh  
S1(config-if-range)#int range g0/1-2  
S1(config-if-range)#sw mode acc  
S1(config-if-range)#sw acc vlan 999  
S1(config-if-range)#sh  
S1(config-if-range)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console  
  

 **S2**   
S2>en  
Password:   
S2#conf t   
Enter configuration commands, one per line. End with CNTL/Z.  
S2(config)#vlan 10  
S2(config-vlan)#name management  
S2(config-vlan)#ex  
S2(config)#vlan 20  
S2(config-vlan)#name Sales  
S2(config-vlan)#ex  
S2(config)#vlan 30  
S2(config-vlan)#name operations  
S2(config-vlan)vlan 999  
S2(config-vlan)#name parking_lot    
S2(config-vlan)#ex  
S2(config-if)#int vlan 10  
S2(config-if)#  
%LINK-5-CHANGED: Interface Vlan10, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up  
ip addr 192.168.10.12 255.255.255.0  
S2(config-if)#ip default-gateway 192.168.10.1  
S2(config-if)# int range g0/1-2   
S2(config-if-range)#sw mode acc  
S2(config-if-range)#sw acc vlan 999  
S2(config-if-range)#sh  
S2(config-if-range)#sw mode acc  
S2(config-if-range)#sw acc vlan 999  
S2(config-if-range)#sh  
S2(config-if-range)#int range f0/19-24  
S2(config-if-range)#sw mode acc  
S2(config-if-range)#sw acc vlan 999  
S2(config-if-range)#sh  
S2(config-if-range)#end 
S2#  
%SYS-5-CONFIG_I: Configured from console by console    
#### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.
a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
b.	Убедитесь, что VLAN назначены на правильные интерфейсы.
**S1**
S1#conf t
S1(config)#int f0/6  
S1(config-if)#sw mode acc  
S1(config-if)#sw acc vlan 20  
S1(config-if)#^Z
%SYS-5-CONFIG_I: Configured from console by console
S1#sh vlan
VLAN Name                             Status    Ports    
---- -------------------------------- --------- -------------------------------    
1    default                          active          
10   management                       active    
20   sales                            active    Fa0/6    
999  parking_lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/7    
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11    
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15    
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19     
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23     
                                                Fa0/24, Gig0/1, Gig0/2    
........................

**S2**
S2(config)#int f0/18
S2(config-if)#sw mode acc
S2(config-if)#sw acc vlan 30
S2(config-if)#^Z
%SYS-5-CONFIG_I: Configured from console by console
S2#sh vlan
VLAN Name                             Status    Ports    
---- -------------------------------- --------- -------------------------------    
1    default                          active        
10   management                       active        
20   Sales                            active      
30   operations                       active    Fa0/18    
999  parking_lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/5    
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9    
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13    
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17    
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22  
                                             Fa0/23, Fa0/24, Gig0/1, Gig0/2 
### Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами
Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.  
a.	Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.  
Откройте окно конфигурации  
b.	Установите native VLAN 1000 на обоих коммутаторах.  
c.	Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.  
d.	Проверьте транки, native VLAN и разрешенные VLAN через транк.  
**S1**  
S1(config)#int f0/1  
S1(config-if)#sw mode tr 
S1(config-if)#  
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down  

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up  

S1(config-if)#sw tr native vlan 1000  
S1(config-if)#sw tr allowed vlan 10,20,30,1000  
S1(config-if)#   
%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on FastEthernet0/1 (1000), with S2 FastEthernet0/1 (1).    
S1(config-if)#^Z  
%SYS-5-CONFIG_I: Configured from console by console  
S1#sh int f0/1 switchport  
Name: Fa0/1  
Switchport: Enabled  
Administrative Mode: trunk  
Operational Mode: trunk  
Administrative Trunking Encapsulation: dot1q  
Operational Trunking Encapsulation: dot1q  
Negotiation of Trunking: On  
Access Mode VLAN: 1 (default)  
Trunking Native Mode VLAN: 1000 (Inactive)  
........  
Trunking VLANs Enabled: 10,20,30,1000  
........  
**S2**  
S2(config)#  
 S2(config-if-range)#int f0/1  
S2(config-if)#sw mode tr   
S2(config-if)#sw tr nat vlan 1000  
S2(config-if)#sw tr allowed vlan 10,20,30,1000  
S2(config-if)#^Z  
%SYS-5-CONFIG_I: Configured from console by console   
S2#sh int f0/1 switchport  
Name: Fa0/1  
Switchport: Enabled  
Administrative Mode: trunk  
Operational Mode: trunk  
Administrative Trunking Encapsulation: dot1q  
Operational Trunking Encapsulation: dot1q  
Negotiation of Trunking: On  
Access Mode VLAN: 1 (default)  
Trunking Native Mode VLAN: 1000 (Inactive)  
..............  
Trunking VLANs Enabled: 10,20,30,1000  
........   
#### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.  
a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.  
b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
c.	Проверка транкинга.  
S1(config-if)#int f0/6  
S1(config-if)#sw mode acc  
S1(config-if)#sw acc vlan 20  
S1(config-if)#do wr  
Building configuration...   
[OK]  
S1#sh int f0/5 sw  
Name: Fa0/5  
Switchport: Enabled  
Administrative Mode: trunk   
Operational Mode: trunk    
Administrative Trunking Encapsulation: dot1q  
Operational Trunking Encapsulation: dot1q  
Negotiation of Trunking: On  
Access Mode VLAN: 1 (default) 
Trunking Native Mode VLAN: 1000 (VLAN1000)   
.........   
Trunking VLANs Enabled: 10,20,30,1000  
......    
**S2 F0/18**  
S2(config)#int f0/18  
S2(config-if)#sw mode acc  
S2(config-if)#sw acc vlan 30  
S2(config-if)#do wr    
Building configuration...     
[OK]    
**Вопрос:**  
Что произойдет, если G0/0/1 на R1 будет отключен?   
**Ответ:**  
Не будет маршрутизации между VLAN.  




### Часть 4. Настройка маршрутизации между сетями VLAN  
#### Шаг 1. Настройте маршрутизатор.  
Откройте окно конфигурации
a.	При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.  
b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.  
c.	Убедитесь, что вспомогательные интерфейсы работают  
R1(config)#int g0/0/1.10   
R1(config-subif)#enc dot1q 10  
R1(config-subif)#enc dot1q 1000 native  
R1(config-subif)#ip addr 192.168.10.1 255.255.255.0  
R1(config-subif)#int g0/0/1.20  
R1(config-subif)#enc dot1q 20  
R1(config-subif)#ip addr 192.168.20.1 255.255.255.0
R1(config-subif)#int g0/0/1.30 
R1(config-subif)#enc dot1q 30  
R1(config-subif)#ip addr 192.168.30.1 255.255.255.0
R1(config-subif)#end  
R1#wr  
Building configuration...  
[OK]  
R1#sh int   
GigabitEthernet0/0/0 is administratively down, line protocol is down (disabled)   
  ........    
GigabitEthernet0/0/1 is up, line protocol is up (connected)    
 ......  
GigabitEthernet0/0/1.10 is up, line protocol is up (connected)  
  ......   
GigabitEthernet0/0/1.20 is up, line protocol is up (connected)  
  Hardware is PQUICC_FEC, address is 00d0.ff87.1802 (bia 00d0.ff87.1802)  
  Internet address is 192.168.20.1/24  
  ........  
  Encapsulation 802.1Q Virtual LAN, Vlan ID 20   
 ......  
GigabitEthernet0/0/1.30 is up, line protocol is up (connected)  
  Hardware is PQUICC_FEC, address is 00d0.ff87.1802 (bia 00d0.ff87.1802)  
  Internet address is 192.168.30.1/24  
  .....  
  Encapsulation 802.1Q Virtual LAN, Vlan ID 30   
  ....  
GigabitEthernet0/0/1.1000 is up, line protocol is up (connected)  
  Hardware is PQUICC_FEC, address is 00d0.ff87.1802 (bia 00d0.ff87.1802)  
  ........  
Vlan1 is administratively down, line protocol is down  
  .......  
### Часть 5. Проверьте, работает ли маршрутизация между VLAN
Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.  
a.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию.  
C:\>ping 192.168.20.1  

Pinging 192.168.20.1 with 32 bytes of data:  

Reply from 192.168.20.1: bytes=32 time=18ms TTL=255  
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255  
Reply from 192.168.20.1: bytes=32 time=1ms TTL=255  
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255  

Ping statistics for 192.168.20.1:  
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
    Minimum = 0ms, Maximum = 18ms, Average = 4ms  
b.	Отправьте эхо-запрос с PC-A на PC-B. 

c.	Отправьте команду ping с компьютера PC-A на коммутатор S2.
Шаг 2. Пройдите следующий тест с PC-B
В окне командной строки на PC-B выполните команду tracert на адрес PC-A.
Вопрос:
Какие промежуточные IP-адреса отображаются в результатах?


                                               



