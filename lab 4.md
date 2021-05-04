# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах 

##Топология 

Таблица адресации
------------
|Устройство |Интерфейс |IPv6-адрес |Длина префикса |Шлюз по умолчанию |
|----------|---------|--------|-------|-------|
| R1	| G0/0/0	| 2001:db8:acad: a::1	|64	|—|
| R1	| G0/0/1	| 2001:db8:acad:1::1 | 	64|	—|
| S1	| VLAN 1	| 2001:db8:acad:1::b	|64	|—|
| PC-A	| NIC	| 2001:db8:acad:1::3	 |64	|fe80::1||
| PC-B	| NIC	| 2001:db8:acad: a::3	 |64	| fe80::1|

## Задачи

### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполните следующие действия:
#### Шаг 1. Настройте маршрутизатор.
Назначьте имя хоста и настройте основные параметры устройства.  
Router>en  
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1  
R1(config)#no ip domain-lookup  

#### Шаг 2. Настройте коммутатор.
Назначьте имя хоста и настройте основные параметры устройства.
Switch>en  
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#sdm prefer dual-ipv4-and-ipv6 default 
Changes to the running SDM preferences have been stored, but cannot take effect until the next reload.  
Use 'show sdm prefer' to see what SDM preference is currently active.  
Switch(config)#do reload  
System configuration has been modified. Save? [yes/no]:y  
Building configuration...  
[OK]  
Proceed with reload? [confirm]y  
Switch>en  
Switch#conf t    
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S1    
S1(config)#no ip domain-lookup  

### Часть 2. Ручная настройка IPv6-адресов    
a.	Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.  
R1>en  
R1#conf t    
R1(config)#int g0/0/0   
R1(config-if)#ipv6 addr 2001:db8:acad: a::1/64   
R1(config-if)#no shut   

R1(config-if)#    
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up      

R1(config-if)#int g0/0/1    
R1(config-if)#ipv6 addr 2001:db8:acad:1::1/64    
R1(config-if)#no shut    
R1(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R1(config-if)#^Z  
R1#   
%SYS-5-CONFIG_I: Configured from console by console   

b.	Введите команду show ipv6 interface brief, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.  
R1#sh ipv6 int br  
GigabitEthernet0/0/0       [up/up]  
    FE80::2E0:F9FF:FE71:5A01  
    2001:DB8:ACAD:A::1  
GigabitEthernet0/0/1       [up/up]  
    FE80::2E0:F9FF:FE71:5A02  
    2001:DB8:ACAD:1::1  
Vlan1                      [administratively down/down]  
    unassigned  
    
c.	Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.  
R1#   
R1#conf t  
Enter configuration commands, one per line.  End with CNTL/Z.  
R1(config)#int g0/0/0  
R1(config-if)#ipv6 addr fe80::1 link-local  
R1(config-if)#int g0/0/1  
R1(config-if)#ipv6 addr fe80::1 link-local  
R1(config-if)#^Z  
R1#  
%SYS-5-CONFIG_I: Configured from console by console  

d.	Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.    
R1#sh ipv6 int brief  
GigabitEthernet0/0/0       [up/up]  
    FE80::1  
    2001:DB8:ACAD:A::1  
GigabitEthernet0/0/1       [up/up]  
    FE80::1  
    2001:DB8:ACAD:1::1  
Vlan1                      [administratively down/down]  
    unassigned  
    
**Вопрос:**  
Какие группы многоадресной рассылки назначены интерфейсу G0/0?  
 **Ответ:** FF02::1:FF00:1 - назначенный адрес, установление соседства  
 
#### Шаг 2. Активируйте IPv6-маршрутизацию на R1.  
a.	В командной строке на PC-B введите команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.  
C:\>ipconfig  

FastEthernet0 Connection:(default port)  

Connection-specific DNS Suffix..:   
Link-local IPv6 Address.........: FE80::260:3EFF:FEBB:385D  
IPv6 Address....................: ::  
Autoconfiguration IPv4 Address..: 169.254.56.93  
Subnet Mask.....................: 255.255.0.0  
Default Gateway.................: ::  
0.0.0.0  
**Вопрос:**
Назначен ли индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B?
Адресов нет поскольку не включена маршрутизация IPv6 и нет DHCP-сервера.
b.	Активируйте IPv6-маршрутизацию на R1 с помощью команды IPv6 unicast-routing, чтобы убедиться, что новая многоадресная группа назначена интерфейсу G0/0/0. Обратите внимание, что в списке групп для интерфейса G0/0 отображается группа многоадресной рассылки всех маршрутизаторов (FF02::2).  
R1(config)#ipv6 unicast-routing
R1(config)#do sh ipv6 int   
GigabitEthernet0/0/0 is up, line protocol is up  
  IPv6 is enabled, link-local address is FE80::1  
  No Virtual link-local address(es):  
  Global unicast address(es):  
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64  
  Joined group address(es):  
    FF02::1  
    FF02::2  
    FF02::1:FF00:1   
   ..........   
**Разбор адресов:**  
FF02::1 - адрес широковещательной рассылки  
FF02::2 - все маршрутизаторы  
FF02::1:FF00:1 - назначенный адрес, установление соседства  
FF02::1:FF71:5A01 - тоже самое, запрошенный адрес 

c.	Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду ipconfig на PC-B. Проверьте данные IPv6-адреса.
C:\>ipconfig  

FastEthernet0 Connection:(default port)  

Connection-specific DNS Suffix..:   
Link-local IPv6 Address.........: FE80::260:3EFF:FEBB:385D  
IPv6 Address....................: 2001:DB8:ACAD:A:260:3EFF:FEBB:385D  
Autoconfiguration IPv4 Address..: 169.254.56.93  
Subnet Mask.....................: 255.255.0.0  
Default Gateway.................: FE80::1  
0.0.0.0

**Вопрос:**
Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?
**Ответ:** протокол SLAAС азначает адреса из той же подсети, что и адрес маршрутизатора.

#### Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.
a.	Назначьте адрес IPv6 для S1. Также назначьте этому интерфейсу локальный адрес канала.
Switch>en
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#int vlan 1
Switch(config-if)#ipv6 addr 2001:db8:acad:1::b/64
Switch(config-if)#ipv6 addr fe80:: link-local
Switch(config-if)#no shut

Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

b.	Проверьте правильность назначения IPv6-адресов интерфейсу управления с помощью команды show ipv6 interface vlan1.
S1# sh ipv6 int  
Vlan1 is up, line protocol is up  
  IPv6 is enabled, link-local address is FE80::  
  No Virtual link-local address(es):  
  Global unicast address(es):  
    2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64  
  Joined group address(es):  
    FF02::1  
    FF02::1:FF00:0  
    FF02::1:FF00:B    
#### Шаг 4. Назначьте компьютерам статические IPv6-адреса.  
a.	Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6.  
b.	Убедитесь, что оба компьютера имеют правильную информацию адреса IPv6. Каждый компьютер должен иметь два глобальных адреса IPv6: один статический и один SLACC  

C:\>ipconfig     

FastEthernet0 Connection:(default port)   

   Connection-specific DNS Suffix..:    
   Link-local IPv6 Address.........: FE80::207:ECFF:FE40:878E     
   IPv6 Address....................: 2001:DB8:ACAD:A::3  
   IPv4 Address....................: 0.0.0.0  
   Subnet Mask.....................: 0.0.0.0  
   Default Gateway.................: FE80::1  
                                     0.0.0.0  

C:\>ipconfig  

FastEthernet0 Connection:(default port)  

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::207:ECFF:FE40:878E  
   IPv6 Address....................: 2001:DB8:ACAD:1::3  
   IPv4 Address....................: 0.0.0.0  
   Subnet Mask.....................: 0.0.0.0  
   Default Gateway.................: FE80::1  
                                     0.0.0.0  
 Не получилось в PT с разными адресами, нет реальной сети на IPv6 чтобы это показать.  
 
### Часть 3. Проверка сквозного подключения  
- С PC-A отправьте эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1.  

C:\>ping fe80::1  

Pinging fe80::1 with 32 bytes of data:  

Reply from FE80::1: bytes=32 time<1ms TTL=255  
Reply from FE80::1: bytes=32 time<1ms TTL=255  
Reply from FE80::1: bytes=32 time<1ms TTL=255  
Reply from FE80::1: bytes=32 time<1ms TTL=255  

Ping statistics for FE80::1:  
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds: 
    Minimum = 0ms, Maximum = 0ms, Average = 0ms  
Отправьте эхо-запрос на интерфейс управления S1 с PC-A.  
C:\>ping  2001:DB8:ACAD:1::B  

Pinging 2001:DB8:ACAD:1::B with 32 bytes of data:  

Reply from 2001:DB8:ACAD:1::B: bytes=32 time=2004ms TTL=255  
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255  
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255  
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255  

Ping statistics for 2001:DB8:ACAD:1::B:  
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
    Minimum = 0ms, Maximum = 2004ms, Average = 501ms  
    
- Введите команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B.

C:\>tracert 2001:db8:acad:a ::3  

Tracing route to 2001:db8:acad:a ::3 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      2001:DB8:ACAD:1 ::1  
  2   0 ms      0 ms      0 ms      2001:DB8:ACAD:A ::3  

Trace complete.  

- С PC-B отправьте эхо-запрос на PC-A.  

C:\>ping 2001:db8:acad:1::3  

Pinging 2001:db8:acad:1::3 with 32 bytes of data:

Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127  
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127  
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127  
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127  

Ping statistics for 2001:DB8:ACAD:1::3:  
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
    Minimum = 0ms, Maximum = 0ms, Average = 0ms  
    
- С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1.

C:\>ping 2001:db8:acad:a::1  

Pinging 2001:db8:acad:a::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD: A::1: bytes=32 time<1ms TTL=255  
Reply from 2001:DB8:ACAD: A::1: bytes=32 time<1ms TTL=255  
Reply from 2001:DB8:ACAD: A::1: bytes=32 time<1ms TTL=255  
Reply from 2001:DB8:ACAD: A::1: bytes=32 time<1ms TTL=255  

Ping statistics for 2001:DB8:ACAD: A::1:  
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:  
    Minimum = 0ms, Maximum = 0ms, Average = 0ms  
    
**Вопросы для повторения**  
1.	Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?  
Link-local адрес и канал заканчиваются на интерфейсе маршрутизатора.
3.	Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64? 
 2001:db8:acad:0::/64


