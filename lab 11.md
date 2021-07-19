# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.  

## Топология  

 ![lab 11](https://user-images.githubusercontent.com/80044182/126212163-74f7ad73-fba4-409d-b33d-9756b925fa6d.png)  

## Таблица адресации    
 |Устройство	|Интерфейс	|IP-адрес	|Маска подсети |	Шлюз по умолчанию|  
|---|---|---|---|---|
|R1	|G0/0/1|	—|	—|	—|
|R1	|G0/0/1.20	|10.20.0.1| 255.255.255.0|	—|
|R1	|G0/0/1.30	|10.30.0.1	|255.255.255.0 |	—|
|R1	|G0/0/1.40	|10.40.0.1	|255.255.255.0	|— |
|R1	|G0/0/1.1000	|—	|—	|—|
|R1	|Loopback1	|172.16.1.1	| 255.255.255.0 |	—|
|R2	|G0/0/1	|10.20.0.4	|255.255.255.0	|—|
|S1	|VLAN 20	|10.20.0.2|	255.255.255.0|	10.20.0.1|
|S2	|VLAN 20	|10.20.0.3	|255.255.255.0	|10.20.0.1|
|PC-A	|NIC	|10.30.0.10	|255.255.255.0	|10.30.0.1|
|PC-B	|NIC	|10.40.0.10	|255.255.255.0	| 10.40.0.1|

## Таблица VLAN
|VLAN	|Имя	|Назначенный интерфейс|
|----|----|----|
|20	|Management	|S2: F0/5|
|30	|Operations	|S1: F0/6|
|40	|Sales|	S2: F0/18|
|999	|ParkingLot	|S1: F0/2-4, F0/7-24, G0/1-2
||S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2|
|1000	|Native|	—|

## Задачи  
Часть 1. Создание сети и настройка основных параметров устройства    
Часть 2. Настройка и проверка списков расширенного контроля доступа  
	Общие сведения и сценарий  
Вам было поручено настроить списки контроля доступа в сети небольшой компании. ACL являются одним из самых простых и прямых средств управления трафиком уровня 3. R1 будет размещать интернет-соединение (смоделированное интерфейсом Loopback 1) и предоставлять информацию о маршруте по умолчанию для R2. После завершения первоначальной настройки компания имеет некоторые конкретные требования к безопасности дорожного движения, которые вы несете ответственность за реализацию. 
 
## Необходимые ресурсы
- 2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)   
- 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)  
- 2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)  
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.  
- Кабели Ethernet, расположенные в соответствии с топологией  
## Инструкции  
### Создание сети и настройка основных параметров устройства  
#### Шаг 1. Создайте сеть согласно топологии.  
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.  
#### Шаг 2. Произведите базовую настройку маршрутизаторов.  
Откройте окно конфигурации   
a.	Назначьте маршрутизатору имя устройства.  
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.  
f.	Зашифруйте открытые пароли.  
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
Закройте окно настройки.  
#### Шаг 3. Настройте базовые параметры каждого коммутатора.  
Откройте окно конфигурации  
a.	Присвойте коммутатору имя устройства.    
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.    
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.    
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.    
f.	Зашифруйте открытые пароли.    
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.   
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.   

Скрипт для **R1**  
en     
conf t  
hostname R1  
no ip domain-lookup  
ser pass  
enable secret class  
banner motd #Unauthorized access is strictly prohibited!#    
line con 0    
logg sync  
password cisco  
login   
line vty 0 15  
password cisco  
login  
exit  
do copy run start  

Результат работы скрипта, далее не приводится полностью. 

Router>en   
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1  
R1(config)#ser pass  
R1(config)#enable secret class  
R1(config)#banner motd #Unauthorized access is strictly prohibited!#   
R1(config)#line con 0  
R1(config-line)#logg sync  
R1(config-line)#password cisco  
R1(config-line)#login    
R1(config-line)#line vty 0 15  
R1(config-line)#password cisco  
R1(config-line)#login   
R1(config-line)#exit  
R1(config)#no ip domain-lookup  
R1(config)#do copy run start  
Destination filename [startup-config]?     
Building configuration...    
[OK]  

**R2** скрипт 
en  
conf t    
hostname R2      
no ip domain-lookup  
ser pass  
enable secret class  
banner motd #Unauthorized access is strictly prohibited!#  
line con 0  
logg sync  
password cisco  
login   
line vty 0 15  
password cisco  
login  
exit  
do copy run start  

В конце enter и отработало успешно.  

**S1** скрипт
en  
conf t    
hostname S1     
no ip domain-lookup  
ser pass  
enable secret class  
banner motd #Unauthorized access is strictly prohibited!#  
line con 0  
logg sync  
password cisco  
login   
line vty 0 15  
password cisco  
login  
exit  
do copy run start  

В конце enter и отработало успешно.  

**S2** скрипт  
en  
conf t    
hostname S2     
no ip domain-lookup  
ser pass  
enable secret class    
banner motd #Unauthorized access is strictly prohibited!#  
line con 0   
logg sync  
password cisco    
login   
line vty 0 15  
password cisco  
login  
exit  
do copy run start  

В конце enter и отработало успешно.   
### Настройка сетей VLAN на коммутаторах.
#### Шаг 1. Создайте сети VLAN на коммутаторах.
Откройте окно конфигурации
a.	Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.
b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 
c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.
Примечание. Команда interface range полезна для выполнения этой задачи с помощью необходимого количества команд.   
#### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.  
a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.  
b.	Выполните команду show vlan brief, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.  
Скрипт для **S1** из привилегированного режима     
conf t    
vlan 20   
name Management  
vlan 30  
name Operations  
vlan 40 
name Sales   
vlan 999  
name Parking_lot  
vlan 1000   
name Native  
!
int vlan 20  
ip addr 10.20.0.2 255.255.255.0    
ip default 10.20.0.1    
!  
int f0/6  
sw mode acc  
sw acc vlan 30  
!  
int range  f0/2-4  
sw mode acc  
sw acc vlan 999  
sh  
!  
int range f0/7-24  
sw mode acc  
sw acc vlan 999  
sh  
! 
int range g0/1-2  
sw mode acc  
sw acc vlan 999  
sh  
exit   
do sh vlan br   

Результат работы скрипта, пропуска команд нет, не брал сообщения о выключении портов.   
S1(config)#do sh vlan br   
 
VLAN Name                             Status    Ports   
---- -------------------------------- --------- -------------------------------  
1    default                          active    Fa0/1, Fa0/5  
20   Management                       active    
30   Operations                       active    Fa0/6   
40   Sales                            active      
999  Parking_lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/7  
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11  
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15  
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19  
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23  
                                                Fa0/24, Gig0/1, Gig0/2  
1000 Native                           active      
1002 fddi-default                     active      
1003 token-ring-default               active      
1004 fddinet-default                  active      
1005 trnet-default                    active      

Скрипт для **S2** из привилегированного режима  

conf t    
vlan 20   
name Management   
vlan 30  
name Operations  
vlan 40  
name Sales   
vlan 999  
name Parking_lot  
vlan 1000    
name Native  
!  
int vlan 20  
ip addr 10.20.0.3 255.255.255.0     
ip default 10.20.0.1    
!   
int f0/18    
sw mode acc    
sw acc vlan 40    
!    
int range  f0/2-4    
sw mode acc  
sw acc vlan 999  
sh  
!   
int range f0/6-17   
sw mode acc  
sw acc vlan 999  
sh  
!
int range f0/19-24  
sw mode acc  
sw acc vlan 999   
sh    
!   
int range g0/1-2  
sw mode acc   
sw acc vlan 999  
sh   
exit  
!  
do sh vlan br  

Результат выполнения скрипта, сообщения о выключении портов не брал.     

S2(config-if)#do sh vlan br    

VLAN Name                             Status    Ports    
---- -------------------------------- --------- -------------------------------    
1    default                          active    Fa0/1, Fa0/5      
20   Management                       active        
30   Operations                       active         
40   Sales                            active    Fa0/18     
999  Parking_lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/6     
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10    
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14    
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19    
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23    
                                                Fa0/24, Gig0/1, Gig0/2     
1000 Native                           active        
1002 fddi-default                     active        
1003 token-ring-default               active        
1004 fddinet-default                  active        
1005 trnet-default                    active         

### Настройте транки (магистральные каналы).   
#### Шаг 1. Вручную настройте магистральный интерфейс F0/1.  
Откройте окно конфигурации  
a.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.  
b.	В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN   сообщения об ошибках могут отображаться временно.  
c.	В качестве другой части конфигурации транка укажите, что VLAN 10, 20, 30 и 1000 разрешены в транке.   
d.	Выполните команду show interfaces trunk для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.    
#### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.  
a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.  
b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.   
c.	Используйте команду show interfaces trunk для проверки настроек транка.   

Скрипт для **S1** из привилегированного режима.
conf t
int f0/1
sw mode tr
sw tr native vlan 1000
sw tr allow vlan 10,20,30,1000
exit
!
int f0/5
sw mode tr
sw tr native vlan 1000
sw tr allow vlan 10,20,30,1000
exit
!
do sh int trunk

Результат работы скрипта.  
S1(config)#do sh int trunk  
Port        Mode         Encapsulation  Status        Native vlan  
Fa0/1       on           802.1q         trunking      1000  
Fa0/5       on           802.1q         trunking      1000  

Port        Vlans allowed on trunk  
Fa0/1       20,30,40,1000  
Fa0/5       20,30,40,1000  

Port        Vlans allowed and active in management domain  
Fa0/1       20,30,40,1000  
Fa0/5       20,30,40,1000   

Port        Vlans in spanning tree forwarding state and not pruned  
Fa0/1       none    
Fa0/5       none    

S1(config)#     
%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on FastEthernet0/1 (1000), with S2 FastEthernet0/1 (1).    

Скрипт для **S2** из привилегированного режима.  
conf t  
int f0/1  
sw mode tr  
sw tr native vlan 1000   
sw tr allow vlan 10,20,30,1000  
exit  
!  
do sh int trunk  

Результат работы скрипта.  
S2(config)#do sh int trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       10,20,30,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       none

### Настройте маршрутизацию.   
#### Шаг 1. Настройка маршрутизации между сетями VLAN на R1.  
Откройте окно конфигурации   
a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.  
b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.   
c.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.  
d.	С помощью команды show ip interface brief проверьте конфигурацию подынтерфейса.  
Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1  

Скрипт для **R1**  

conf t  
int g0/0/1  
no ip addr  
no sh  
!  
int g0/0/1.20   
enc dot1q 20  
ip addr 10.20.0.1 255.255.255.0  
!  
int g0/0/1.30  
enc dot1q 30  
ip addr 10.30.0.1 255.255.255.0  
!  
int g0/0/1.40  
enc dot1q 40  
ip addr 10.40.0.1 255.255.255.0  
!  
int g0/0/1.1000  
enc dot1q 1000 native  
!  
int lo 1   
no sh  
ip addr 172.16.1.1 255.255.255.0  
exit   
!  
do sh ip int br  

Результат работы скрипта 
 
R1(config)#do sh ip int br  
Interface IP-Address OK? Method Status Protocol   
GigabitEthernet0/0/0 unassigned YES unset administratively down down   
GigabitEthernet0/0/1 unassigned YES unset up up   
GigabitEthernet0/0/1.2010.20.0.1 YES manual up up   
GigabitEthernet0/0/1.3010.30.0.1 YES manual up up   
GigabitEthernet0/0/1.4010.40.0.1 YES manual up up   
GigabitEthernet0/0/1.1000unassigned YES unset up up     
Loopback1 172.16.1.1 YES manual up up     
Vlan1 unassigned YES unset administratively down down  

**R2**
R2(config)#conf t  
R2(config)#ip route 0.0.0.0 0.0.0.0 10.20.0.1  
R2(config)#int g0/0/1  
R2(config-if)#ip addr 10.20.0.4 255.255.255.0  
R2(config-if)#exit  

### Настройте удаленный доступ   
Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.   
Откройте окно конфигурации  
a.	Создайте локального пользователя с именем пользователя SSHadmin и зашифрованным паролем $cisco123!  
b.	Используйте ccna-lab.com в качестве доменного имени.    
c.	Генерируйте криптоключи с помощью 1024 битного модуля.    
d.	Настройте первые пять линий VTY на каждом устройстве, чтобы поддерживать только SSH-соединения и с локальной аутентификацией.    
Универсальный скрипт  
conf t  
username SSHadmin secret $cisco123!  
ip domain name ccna-lab.com  
crypto key gen rsa   
1024   
ip ssh ver 2
line vty 0 5
tr input ssh
login local
exit

Результат работы скрипта на **S1**

S1#conf t  
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#username SSHadmin secret $cisco123!  
S1(config)#ip domain name ccna-lab.com  
S1(config)#crypto key gen rsa   
The name for the keys will be: S1.ccna-lab.com  
Choose the size of the key modulus in the range of 360 to 2048 for your  
  General Purpose Keys. Choosing a key modulus greater than 512 may take  
  a few minutes.  

How many bits in the modulus [512]: 1024   
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]  

S1(config)#ip ssh ver 2  
Mar 1 1:13:49.752: %SSH-5-ENABLED: SSH 1.99 has been enabled  
S1(config)#line vty 0 5  
S1(config-line)#tr input ssh  
S1(config-line)#login local  
S1(config-line)#exit  

Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.    
a.	Включите сервер HTTPS на R1.    
R1(config)# ip http secure-server     
b.	Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.  
R1(config)# ip http authentication local    
Не работает. Нет в PT, но проверено на стороне.   

### Проверка подключения  
Шаг 1. Настройте узлы ПК.  
Адреса ПК можно посмотреть в таблице адресации.  
Шаг 2. Выполните следующие тесты. Эхо запрос должен пройти успешно.  
Примечание. Возможно, вам придется отключить брандмауэр ПК для работы ping  
От	Протокол	Назначение  
PC-A	Ping	10.40.0.10  
PC-A	Ping	10.20.0.1  
PC-B	Ping	10.30.0.10  
PC-B	Ping	10.20.0.1  
PC-B	Ping	172.16.1.1  
PC-B	HTTPS	10.20.0.1  
PC-B	HTTPS	172.16.1.1  
PC-B	SSH	10.20.0.1  
PC-B	SSH	172.16.1.1  

**PC-A**  
Pinging 10.20.0.1 with 32 bytes of data:   

Reply from 10.20.0.1: bytes=32 time=1ms TTL=255   
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255  
  
Ping statistics for 10.20.0.1:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 1ms, Average = 0ms  

C:\>ping 10.40.0.1  

Pinging 10.40.0.1 with 32 bytes of data:  

Reply from 10.40.0.1: bytes=32 time<1ms TTL=255   
Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  

Ping statistics for 10.40.0.1:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:   
Minimum = 0ms, Maximum = 0ms, Average = 0ms  
 
**PC-B**  
Pinging 10.20.0.1 with 32 bytes of data:  

Reply from 10.20.0.1: bytes=32 time=1ms TTL=255  
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255  

Ping statistics for 10.20.0.1:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 1ms, Average = 0ms  

C:\>ping 10.40.0.1  

Pinging 10.40.0.1 with 32 bytes of data:  

Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  
Reply from 10.40.0.1: bytes=32 time<1ms TTL=255  

Ping statistics for 10.40.0.1:  
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
Minimum = 0ms, Maximum = 0ms, Average = 0ms  

SSH 172.16.1.1  
Password: $cisco123!  

Unauthorized access is strictly prohibited!  

R1>  

SSH 10.20.0.1  
Password: $cisco123!  

Unauthorized access is strictly prohibited!  

R1>  
 
### Настройка и проверка списков контроля доступа (ACL)  
При проверке базового подключения компания требует реализации следующих политик безопасности:   
Политика1. Сеть Sales не может использовать SSH в сети Management (но в  другие сети SSH разрешен).    

Политика 2. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales  может получить доступ к интерфейсу Loopback 1 на R1).

Политика3. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам.   

Политика 4: Cеть Operations  не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам. 

Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.    
Loopback 1 здесь заменяет WAN, допущение в том, что в этой сети могут использоваться любые протоколы. В реальной сети стоит оставить вовне только Web и используемые протоколы. Остальное запретить. Внутри сети опять же только используемые протоколы в нужных направлениях. Межсетевой экран лучше подходит для этих задач особенно на границе периметра сети.  
Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.  
Откройте окно конфигурации.    
 
Закройте окно настройки.  
Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.  
Выполните следующие тесты. Ожидаемые результаты показаны в таблице:  
От	Протокол	Назначение	Результат  
PC-A	Ping	10.40.0.10	Сбой  
PC-A	Ping	10.20.0.1	Успех  
PC-B	Ping	10.30.0.10	Сбой  
PC-B	Ping	10.20.0.1	Сбой  
PC-B	Ping	172.16.1.1	Успех  
PC-B	HTTPS	10.20.0.1	Сбой  
PC-B	HTTPS	172.16.1.1	Успех  
PC-B	SSH	10.20.0.4	Сбой  
PC-B	SSH	172.16.1.1	Успех  









