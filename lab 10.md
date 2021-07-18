# Лабораторная работа. Настройка протокола OSPFv2 для одной области

## Топология
 
## Таблица адресации
|Устройство |Интерфейс	|IP-адрес	|Маска подсети |
|----|-----|--------|----------|
|R1	|G0/0/1	|10.53.0.1  	|255.255.255.0 |
|R1	| Loopback1| 172.16.1.1| 	255.255.255.0|
|R2	|G0/0/1	|10.53.0.2 	|255.255.255.0|
|R2	|Loopback1 |	192.168.1.1| 	255.255.255.0|
## Цели 
Часть 1. Создание сети и настройка основных параметров устройства   
Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области  
Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области  
## Общие сведения и сценарий  
Вам было поручено настроить сеть небольшой компании с помощью OSPFv2. R1 будет размещать интернет-соединение (имитируемое интерфейсом Loopback 1) и делиться   информацией о маршруте по умолчанию до  R2. После первоначальной настройки организация попросила оптимизировать конфигурацию, чтобы уменьшить трафик протокола и   гарантировать, что R1 продолжает контролировать маршрутизацию.  
## Необходимые ресурсы  
-	2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)  
-	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)  
-	1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)  
-	Консольные кабели для настройки устройств Cisco IOS через консольные порты.  
-	Кабели Ethernet, расположенные в соответствии с топологией  
## Инструкции  
### Часть 1. Создание сети и настройка основных параметров устройства
#### Шаг 1. Создайте сеть согласно топологии.  
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.   
#### Шаг 2. Произведите базовую настройку маршрутизаторов.  
#### Откройте окно конфигурации
a.	Назначьте маршрутизатору имя устройства.  
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте ciscoв качестве пароля консоли и включите вход в систему по паролю.  
e.	Назначьте ciscoв качестве пароля VTY и включите вход в систему по паролю.  
f.	Зашифруйте открытые пароли.  
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
Шаг 3. Настройте базовые параметры каждого коммутатора.  
a.	Назначьте коммутатору имя устройства.  
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
d.	Назначьте ciscoв качестве пароля консоли и включите вход в систему по паролю.  
e.	Назначьте ciscoв качестве пароля VTY и включите вход в систему по паролю.  
f.	Зашифруйте открытые пароли.  
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.  
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  
Закройте окно настройки.  
**R1**  
Router>en  
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1  
R1(config)#no ip domain lookup  
R1(config)#banner motd #Unauthorized acces is strictly prohibited!#  
R1(config)#ser pass  
R1(config)#enable secret class  
R1(config)#line con 0  
R1(config-line)#pass cisco    
R1(config-line)#logg sync  
R1(config-line)#login  
R1(config-line)#line vty 0 4  
R1(config-line)#pass cisco  
R1(config-line)#login   
R1(config-line)#exit  
R1(config)#do copy run start  
Destination filename [startup-config]?   
Building configuration...  
[OK]  
**S1**  
Switch>en     
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S1  
S1(config)#no ip domain lookup  
S1(config)#banner motd #Unauthorized access is strictly prohibited!#  
S1(config)#  
S1(config)#enable secret class  
S1(config)#line con 0 
S1(config-line)#pass cisco  
S1(config-line)#logg sync  
S1(config-line)#login  
S1(config-line)#line vty 0 4  
S1(config-line)#pass cisco  
S1(config-line)#login  
S1(config-line)#exit  
**S2**  
Switch>en   
Switch#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Switch(config)#hostname S2  
S2(config)#no ip domain lookup  
S2(config)#banner motd #Unauthorized access is strictly prohibited!#  
S2(config)#enable secret class  
S2(config)#line con 0  
S2(config-line)#pass cisco  
S2(config-line)#logg sync  
S2(config-line)#login  
S2(config-line)#line vty 0 4  
S2(config-line)#pass cisco  
S2(config-line)#login  
S2(config-line)#exit  
Router>en  
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R2  
R2(config)#no ip domain lookup  
R2(config)#banner motd #Unauthorized acces is strictly prohibited!#  
R2(config)#ser pass  
R2(config)#enable secret class  
R2(config)#line con 0  
R2(config-line)#pass cisco  
R2(config-line)#logg sync  
R2(config-line)#login  
R2(config-line)#line vty 0 4  
R2(config-line)#pass cisco  
R2(config-line)#login  
R2(config-line)#exit  
R2(config)#do copy run start  
Destination filename [startup-config]?   
Building configuration...  
[OK]  
### Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области  
#### Шаг 1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.  
a.	Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.  
Откройте окно конфигурации  
b.	Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.  
c.	Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).  
d.	Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.   
e.	Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.    
f.	Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.    
**Вопрос:**   
Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора? 
**Ответ:**  
DR - R2 из-за большего ID, BDR R1.  

g.	На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по   умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.  
h.	Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.  
Закройте окно настройки.   
**R1**
R1(config)#int loop 1  
R1(config-if)#  
%LINK-5-CHANGED: Interface Loopback1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up  

R1(config-if)#ip addr 172.16.1.1 255.255.255.0  
R1(config-if)#int g0/0/1  
R1(config-if)#ip addr 10.53.0.1 255.255.255.0  
R1(config-if)#no sh  

R1(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up  
R1(config-if)#exit   
R1(config)#router ospf 56 
R1(config-router)#router-id 1.1.1.1  
R1(config-router)#network 10.53.0.0  0.0.0.255 area 0  
R1(config-router)#exit  
R1(config)#do clear ip ospf process    
Reset ALL OSPF processes? [no]: y  
R1(config)# 
01:15:41: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done  
R1(config)#do sh ip route ospf  
192.168.1.0/32 is subnetted, 1 subnets  
O 192.168.1.1 [110/2] via 10.53.0.2, 00:04:20, GigabitEthernet0/0/1  
R1(config)#do ping 192.168.1.1  

Type escape sequence to abort.  
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:  
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

  **R2**
R2(config)#int loop 1  

R2(config-if)#  
%LINK-5-CHANGED: Interface Loopback1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up  

R2(config-if)#ip addr 192.168.1.1   
R2(config-if)#ip addr 192.168.1.1 255.255.255.0  
R2(config-if)#int g0/0/1    
R2(config-if)#ip addr 10.53.0.2   
R2(config-if)#ip addr 10.53.0.2 255.255.255.0    
R2(config-if)#no sh  
R2(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up  

R2(config-if)#exit  
R2(config)#router ospf 56 area 0  
R2(config-router)#router 2.2.2.2  
R2(config-router)#network 192.168.1.0 0.0.0.255 area 0  
R2(config-router)#exit  


### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области
#### Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.
Откройте окно конфигурации
a.	На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.  
b.	Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.  
c.	На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.  
d.	добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.  
e.	Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.  
f.	Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды clear ip ospf process . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.  
**R1**   
R1(config)#int g0/0/1   
R1(config-if)#ip ospf hello 30    
R1(config-if)#ip ospf priority 50   
R1(config-if)#ip ospf dead 120    
R1(config-if)#ex   
R1(config)#ip route 0.0.0.0 0.0.0.0 loop 1    
%Default route without gateway, if not a point-to-point interface, may impact performance  
R1(config)#router ospf 56  
R1(config-router)#def origin 
R1(config-router)#auto-cost reference-bandwidth 1000    
% OSPF: Reference bandwidth is changed.    
        Please ensure reference bandwidth is consistent across all routers.
R1(config-router)#do clear ip ospf proc
Reset ALL OSPF processes? [no]: y

R1(config-router)#  
00:15:17: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Adjacency forced to reset     

00:15:17: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Interface down or detached   

R1(config-router)#  
00:15:30: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done    
  
R2(config-router)#exit 

**R2**  
R2(config)#int g0/0/1  
R2(config-if)#ip ospf hello 30  
R2(config-if)#ip ospf dead 120  
R2(config-if)#ex  
R2(config)#router ospf 56  
R2(config-router)#passive lo 1 
R2(config-router)#auto-cost reference-bandwidth 1000  
% OSPF: Reference bandwidth is changed.  
        Please ensure reference bandwidth is consistent across all routers.
R2(config-router)#do clear ip ospf proc
Reset ALL OSPF processes? [no]: y

R2(config-router)#
00:15:28: %OSPF-5-ADJCHG: Process 56, Nbr 1.1.1.1 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Adjacency forced to reset

00:15:28: %OSPF-5-ADJCHG: Process 56, Nbr 1.1.1.1 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Interface down or detached

R2(config-router)#
00:15:30: %OSPF-5-ADJCHG: Process 56, Nbr 1.1.1.1 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done

R2(config-router)#ex  
R2(config)#int lo 1  
R2(config-if)#ip ospf network point-to-point  
R2(config-if)#ex  

#### Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.
a.	Выполните команду show ip ospf interface g0/0/1 на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast  
b.	На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.  
c.	Введите команду showip route ospf на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.  
d.	Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.  
**R1**  
R1(config)#do sh ip ospf int g0/0/1  

GigabitEthernet0/0/1 is up, line protocol is up  
  Internet address is 10.53.0.1/24, Area 0  
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10  
  Transmit Delay is 1 sec, State DR, Priority 50  
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1  
  Backup Designated Router (ID) 2.2.2.2, Interface address 10.53.0.2  
  Timer intervals configured, Hello 30, Dead 120, Wait 40, Retransmit 5   
    Hello due in 00:00:09  
  Index 1/1, flood queue length 0  
  Next 0x0(0)/0x0(0)  
  Last flood scan length is 1, maximum is 1    
  Last flood scan time is 0 msec, maximum is 0 msec    
  Neighbor Count is 1, Adjacent neighbor count is 1    
    Adjacent with neighbor 2.2.2.2  (Backup Designated Router)    
  Suppress hello for 0 neighbor(s) 
  
R1#sh ip route ospf
     192.168.1.0/32 is subnetted, 1 subnets
O       192.168.1.1 [110/2] via 10.53.0.2, 00:01:02, GigabitEthernet0/0/1
  
  R1(config)#do ping 192.168.1.1    

Type escape sequence to abort.  
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:  
!!!!!  
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/2 ms
**R2**   
R2(config)#do sh ip route ospf  
O*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:01:49, GigabitEthernet0/0/1   
 

**Вопрос:**
Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?  
**Ответ:**  
Стоимость прямо зависит от пропускной способности задействованных интрефейсов и от базовой пропускной способности маршрутизатора. Пропускная способность виртуального интерфейса равна базовой. 
