# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах 

##Топология 

Таблица адресации
------------
|Устройство |Интерфейс |IPv6-адрес |Длина префикса |Шлюз по умолчанию |
|----------|---------|--------|-------|-------|
| R1	| G0/0/0	| 2001:db8:acad:a::1	|64	|—|
| R1	| G0/0/1	| 2001:db8:acad:1::1 | 	64|	—|
| S1	| VLAN 1	| 2001:db8:acad:1::b	|64	|—|
| PC-A	| NIC	| 2001:db8:acad:1::3	 |64	|fe80::1||
| PC-B	| NIC	| 2001:db8:acad:a::3	 |64	| fe80::1|

## Задачи

###Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

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

Router>en  
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1
R1(config)#no ip domain-lookup  
R1(config)#ipv6 unicast-routing  

###Часть 2. Ручная настройка IPv6-адресов

S1>en  
S1#conf t    
S1(config)#int vlan 1   
S1(config-if)#ipv6 addr 2001:db8:acad:1::b/64  
S1(config-if)#no shut  

Switch(config-if)#  
%LINK-5-CHANGED: Interface Vlan1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up  
S1(config-if)#^Z
S1# 
%SYS-5-CONFIG_I: Configured from console by console 

S1>en  
S1#conf t    
R1(config)#int g0/0/0   
R1(config-if)#ipv6 addr 2001:db8:acad:a::1/64   
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


###Часть 3. Проверка сквозного соединения

### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполните следующие действия:
Шаг 1. Настройте маршрутизатор.
Назначьте имя хоста и настройте основные параметры устройства.
Шаг 2. Настройте коммутатор.
Назначьте имя хоста и настройте основные параметры устройства.
