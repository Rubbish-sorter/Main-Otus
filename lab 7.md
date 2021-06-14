# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
## Топология
 ![lab 7](https://user-images.githubusercontent.com/80044182/121935273-eb148380-cd50-11eb-9e8c-f4b54f87296a.png)
## Таблица адресации
|Устройство |Интерфейс	|IP-адрес	|Маска подсети|
|--|----|-----|-----|
|S1	|VLAN 1	|192.168.1.1	|255.255.255.0|
|S2	| VLAN 1	|192.168.1.2	|255.255.255.0 |
|S3	|VLAN 1	|192.168.1.3 |	255.255.255.0|
##	Цели
### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Выбор корневого моста
### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
	
## Необходимые ресурсы
-	3 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты
-	Кабели Ethernet, расположенные в соответствии с топологией
### Часть 1:	Создание сети и настройка основных параметров устройства
#### Шаг 1:	Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
#### Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.
#### Шаг 3:	Настройте базовые параметры каждого коммутатора.
a.	Отключите поиск DNS.  
b.	Присвойте имена устройствам в соответствии с топологией.  
c.	Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.  
d.	Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.  
e.	Настройте logging synchronous для консольного канала.  
f.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.  
g.	Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.  
h.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.  
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
S1(config-line)#int vlan 1  
S1(config-if)#no sh  
%LINK-5-CHANGED: Interface Vlan1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up  
S1(config-if)#ip addr 192.168.1.1 255.255.255.0  
S1(config-if)#^Z  
S1# 
%SYS-5-CONFIG_I: Configured from console by console  
S1#copy run start 
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
S2(config-line)#int vlan 1    
S2(config-if)#no sh     
%LINK-5-CHANGED: Interface Vlan1, changed state to up     

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up       
S2(config-if)#ip addr 192.168.1.1 255.255.255.0      
S2(config-if)#^Z    
%SYS-5-CONFIG_I: Configured from console by console  
S2#copy run start    
Building configuration...    
[OK]   
**S3**
Switch>en  
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S3  
S3(config)#banner motd #Unauthorized access is strictly prohibited!#  
S3(config)#ser pass  
S3(config)#enable secret class  
S3(config)#no ip domain lookup  
S3(config)#line con 0  
S3(config-line)#pass cisco  
S3(config-line)#login  
S3(config-line)#line vty 0 4  
S3(config-line)#pass cisco  
S3(config-line)#login  
S3(config-line)#int vlan 1    
S3(config-if)#no sh     
%LINK-5-CHANGED: Interface Vlan1, changed state to up     

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up       
S3(config-if)#ip addr 192.168.1.1 255.255.255.0      
S3(config-if)#^Z    
%SYS-5-CONFIG_I: Configured from console by console  
S3#copy run start    
Building configuration...    
[OK]   
Шаг 4:	Проверьте связь.  
Проверьте способность компьютеров обмениваться эхо-запросами.  
Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	____3/5_________  
Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?	_____3/5_________  
Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?	________3/5______  
Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными. 
Часть 2:	Определение корневого моста    
Шаг 1:	Отключите все порты на коммутаторах.  
Шаг 2:	Настройте подключенные порты в качестве транковых.  
Шаг 3:	Включите порты F0/2 и F0/4 на всех коммутаторах. 
**S1**   
S1#conf t    
Enter configuration commands, one per line. End with CNTL/Z.  
S1(config)#int range f0/1-24  
S1(config-if-range)#sh  

%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down  

------------------  

S1(config-if-range)#int range g0/1-2  
S1(config-if-range)#sh  

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down  
 
%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down  
S1(config-if-range)#int range f0/1-4  
S1(config-if-range)#sw mode tr  
S1(config-if-range)int f0/2  
S1(config-if)#no sh  
%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to up  
 
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up  
S1(config-if)#int f0/4  
S1(config-if)#no sh  
S1(config-if)#  
%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to up    

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/4, changed state to up  
**S2**  
S2#conf t    
Enter configuration commands, one per line. End with CNTL/Z.  
S2(config)#int range f0/1-24  
S2(config-if-range)#sh  

%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down  

------------------  

S2(config-if-range)#int range g0/1-2  
S2(config-if-range)#sh  

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down  
 
%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down  
S2(config-if-range)#int range f0/1-4  
S2(config-if-range)#sw mode tr  
S2(config-if-range)int f0/2  
S2(config-if)#no sh  
%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to up  
 
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up  
S2(config-if)#int f0/4  
S2(config-if)#no sh  
S2(config-if)#  
%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to up    

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/4, changed state to up   

**S3**  
S3#conf t    
Enter configuration commands, one per line. End with CNTL/Z.  
S3(config)#int range f0/1-24  
S3(config-if-range)#sh  

%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down  

------------------  

S3(config-if-range)#int range g0/1-2  
S3(config-if-range)#sh  

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down  
 
%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down  
S3(config-if-range)#int range f0/1-4  
S3(config-if-range)#sw mode tr  
S3(config-if-range)int f0/2  
S3(config-if)#no sh  
%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to up  
 
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up  
S3(config-if)#int f0/4  
S3(config-if)#no sh  
S3(config-if)#  
%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to up    

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/4, changed state to up  

#### Шаг 4: Отобразите данные протокола spanning-tree.  
S1#sh spanning-tree  
VLAN0001  
Spanning tree enabled protocol ieee  
Root ID Priority 32769  
Address 000A.F366.6673  
This bridge is the root  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 000A.F366.6673  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  

Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Desg FWD 19 128.2 P2p  
Fa0/4 Desg FWD 19 128.4 P2p  

S2#sh spanning-tree   
VLAN0001  
Spanning tree enabled protocol ieee   
Root ID Priority 32769  
Address 000A.F366.6673  
Cost 19  
Port 2(FastEthernet0/2)  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 00D0.9749.65D7  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  

Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Root FWD 19 128.2 P2p  
Fa0/4 Altn BLK 19 128.4 P2p  

S3#sh spanning-tree  
VLAN0001  
Spanning tree enabled protocol ieee  
Root ID Priority 32769   
Address 000A.F366.6673  
Cost 19  
Port 4(FastEthernet0/4)  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 0060.3E50.0610  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  


Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Desg FWD 19 128.2 P2p  
Fa0/4 Root FWD 19 128.4 P2p  
![lab 7 1](https://user-images.githubusercontent.com/80044182/121953568-659bce00-cd66-11eb-8091-3d3a8f54a1a5.png)
С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.  
Какой коммутатор является корневым мостом?   
_S1_____________  
Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста? 
_______Наименьший MAC-адрес.         
Какие порты на коммутаторе являются корневыми портами?  
____Нет.______________________  
Какие порты на коммутаторе являются назначенными портами?  
______F0/2, F0/4________________  
Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?    
___________F0/4 на __S2______  
Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?  
_Есть лучший маршрут до корневого коммутатора.___________________________________________  
### Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
#### Шаг 1:	Определите коммутатор с заблокированным портом.
При текущей конфигурации только один коммутатор может содержать заблокированный протоколом STP порт. Выполните команду show spanning-tree на обоих коммутаторах некорневого моста. 
S2#sh spanning-tree   
VLAN0001  
Spanning tree enabled protocol ieee   
Root ID Priority 32769  
Address 000A.F366.6673  
Cost 19  
Port 2(FastEthernet0/2)  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 00D0.9749.65D7  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  

Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Root FWD 19 128.2 P2p  
Fa0/4 Altn BLK 19 128.4 P2p  

S3#sh spanning-tree  
VLAN0001  
Spanning tree enabled protocol ieee  
Root ID Priority 32769   
Address 000A.F366.6673  
Cost 19  
Port 4(FastEthernet0/4)  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 0060.3E50.0610  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  


Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Desg FWD 19 128.2 P2p  
Fa0/4 Root FWD 19 128.4 P2p  

#### Шаг 2:	Измените стоимость порта.
Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста.   Уменьшите стоимость этого порта корневого моста до 18, выполнив команду spanning-tree cost 18 режима конфигурации интерфейса.  
S2(config)# interface f0/4  
S2(config-if)# spanning-tree cost 18  
Шаг 3:	Просмотрите изменения протокола spanning-tree.  
Повторно выполните команду show spanning-tree на обоих коммутаторах некорневого моста.
S2#sh spanning-tree   
VLAN0001  
Spanning tree enabled protocol ieee   
Root ID Priority 32769  
Address 000A.F366.6673  
Cost 19  
Port 2(FastEthernet0/2)  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 00D0.9749.65D7  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  

Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Root FWD 19 128.2 P2p  
Fa0/4 Altn BLK 19 128.4 P2p  

S3#sh spanning-tree  
VLAN0001  
Spanning tree enabled protocol ieee  
Root ID Priority 32769   
Address 000A.F366.6673  
Cost 19  
Port 4(FastEthernet0/4)  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  

Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)  
Address 0060.3E50.0610  
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec  
Aging Time 20  


Interface Role Sts Cost Prio.Nbr Type  
---------------- ---- --- --------- -------- --------------------------------
Fa0/2 Desg FWD 19 128.2 P2p  
Fa0/4 Root FWD 19 128.4 P2p  

