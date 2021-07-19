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






