# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора 
## Топология
![lab 2](https://user-images.githubusercontent.com/80044182/112767378-21c6e180-901f-11eb-8913-cc6c9e05c04a.png)
| Устройство  |  Интерфейс | IP-адрес/префикс  | 
| ------------ | ------------ | ------------ |
| S1| VLAN 1   | 192.168.1.11/24 |
| S2| VLAN 1   | 192.168.1.12 /24 |
|  PC-A | NIC  | 192.168.1.1 /24  | 
|  PC-B | NIC  | 192.168.1.2 /24  |

## Цели
  ### Часть 1. Создание и настройка сети
  ### Часть 2. Изучение таблицы МАС-адресов коммутатора
## Необходимые ресурсы
-	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
-	2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)
-	Консольные кабели для настройки устройств Cisco IOS через консольные порты.
-	Кабели Ethernet, расположенные в соответствии с топологией
## Инструкции
### Часть 1. Создание и настройка сети
#### Шаг 1. Подключите сеть в соответствии с топологией.
#### Шаг 2. Настройте узлы ПК.
#### Шаг 3. Выполните инициализацию и перезагрузку коммутаторов.
#### Шаг 4. Настройте базовые параметры каждого коммутатора.
Откройте окно конфигурации

  a.	Настройте имена устройств в соответствии с топологией.
  
  b.	Настройте IP-адреса, как указано в таблице адресации.
  
  c.	Назначьте cisco в качестве паролей консоли и VTY.
  
  d.	Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.
  
Настройка S1, S2 тоже саме за исключением IP адреса и имени. 
Switch>enable

Switch#conf t

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#hostname S1

S1(config)#ser pass

S1(config)#no ip domain-lookup

S1(config)#enable secret class

S1(config)#banner motd #Unauthorized access is strictly prohibited.#

S1(config)#line con 0

S1(config-line)#logging sync

S1(config-line)#pass cisco

S1(config-line)#login

S1(config-line)#exit

S1(config)#line vty 0 15

S1(config-line)#pass cisco

S1(config-line)#login

S1(config-line)#exit

S1(config)#int vlan 1

S1(config-if)#ip addr 192.168.1.11 255.255.255.0

S1(config-if)#ip default-gateway 192.168.1.0

S1(config-if)#no shutdown

S1(config-if)#end

S1#

%SYS-5-CONFIG_I: Configured from console by console

### Часть 2. Изучение таблицы МАС-адресов коммутатора
#### Шаг 1. Запишите МАС-адреса сетевых устройств.

a.	Откройте командную строку на PC-A и PC-B и введите команду ipconfig /all.

PC-A

C:\>ipconfig /all

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: 

Physical Address................: 000D.BDE3.10C6

Link-local IPv6 Address.........: FE80::20D:BDFF:FEE3:10C6

IPv6 Address....................: ::

IPv4 Address....................: 192.198.1.1

Subnet Mask.....................: 255.255.255.0

Default Gateway.................: ::

192.168.1.0

DHCP Servers....................: 0.0.0.0

DHCPv6 IAID.....................: 

DHCPv6 Client DUID..............: 00-01-00-01-9B-35-EB-2E-00-0D-BD-E3-10-C6

DNS Servers.....................: ::

0.0.0.0

**Вопрос:**
Назовите физические адреса адаптера Ethernet.
MAC-адрес компьютера PC-A:000D.BDE3.10C6
MAC-адрес компьютера PC-B: 0001.638A.217B (вывод команды не приводится)
Закройте окно командной строки.
b.	Подключитесь к коммутаторам S1 и S2 через консоль и введите команду show interface F0/1 на каждом коммутаторе.
Откройте окно конфигурации

S1#sh int f0/1

FastEthernet0/1 is up, line protocol is up (connected)

Hardware is Lance, address is 00e0.8f3d.8e01 (bia 00e0.8f3d.8e01)

BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255

Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s

input flow-control is off, output flow-control is off

ARP type: ARPA, ARP Timeout 04:00:00
.............

Вопросы:
Назовите адреса оборудования во второй строке выходных данных команды (или зашитый адрес — bia).

МАС-адрес коммутатора S1 Fast Ethernet 0/1: 00e0.8f3d.8e01

МАС-адрес коммутатора S2 Fast Ethernet 0/1: 0001.4355.6001

#### Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.
Подключитесь к коммутатору S2 через консоль и просмотрите таблицу МАС-адресов до и после тестирования сетевой связи с помощью эхо-запросов.
a.	Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC.
Откройте окно конфигурации
b.	В привилегированном режиме EXEC введите команду show mac address-table и нажмите клавишу ввода.

S2#sh mac addr

          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1   00e0.8f3d.8e01    DYNAMIC     Fa0/1

Даже если сетевая коммуникация в сети не происходила (т. е. если команда ping не отправлялась), коммутатор может узнать МАС-адреса при подключении к ПК и другим коммутаторам.
**Вопросы:**
Записаны ли в таблице МАС-адресов какие-либо МАС-адреса? Да, адрес интерфейса соседнего коммутатора

Какие МАС-адреса записаны в таблице? С какими портами коммутатора они сопоставлены и каким устройствам принадлежат? Игнорируйте МАС-адреса, сопоставленные с центральным процессором.  00e0.8f3d.8e01 - адрес порта Fa0/1 коммутатора S1 и сопоставлен с портом Fa0/1.

Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды show mac address-table? Работает ли это решение в любой ситуации? Нет поскольку неизвестно что увидит коммутатор по протоколу ARP в большой сети и хост может оказаться за следующим коммутатором.

#### Шаг 3. Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.
a.	В привилегированном режиме EXEC введите команду clear mac address-table dynamic и нажмите клавишу Enter.

S2#clear mac address-table dynamic

S2#show mac address-table

          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1   00e0.8f3d.8e01    DYNAMIC     Fa0/1
b.	Снова быстро введите команду show mac address-table.

S2#show mac address-table

          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1   00e0.8f3d.8e01    DYNAMIC     Fa0/1

Вопросы:
Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса? Да, адрес интерфейса соседнего коммутатора. На реальном оборудовании должно быть пусто если не ошибаюсь.
Через 10 секунд введите команду show mac address-table и нажмите клавишу ввода. Появились ли в таблице МАС-адресов новые адреса? Нет, есть вопросы к работе Packet Tracer.

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1   00e0.8f3d.8e01    DYNAMIC     Fa0/1

Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.
a.	На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.
Откройте командную строку.
Вопрос:
Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?
b.	Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time=2ms TTL=128

Reply from 192.168.1.1: bytes=32 time=10ms TTL=128

Reply from 192.168.1.1: bytes=32 time<1ms TTL=128

Reply from 192.168.1.1: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 10ms, Average = 3ms

C:\>ping 192.168.1.11

Pinging 192.168.1.11 with 32 bytes of data:

Request timed out.

Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Reply from 192.168.1.11: bytes=32 time=13ms TTL=255

Ping statistics for 192.168.1.11:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 13ms, Average = 4ms

C:\>ping 192.168.1.12

Pinging 192.168.1.12 with 32 bytes of data:

Request timed out.

Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.12:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

**Вопрос:**

От всех ли устройств получены ответы? Да, кое-где были потери пакетов в момент отправки ARP запросов.

c.	Подключившись через консоль к коммутатору S2, введите команду show mac address-table.

Mac address table

------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    00e0.8f3d.8e01    DYNAMIC     Fa0/1
   
**Вопрос:**

Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства? Нет, нет новых адресов.

На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

C:\>arp -a

  Internet Address      Physical Address      Type
  
  192.168.1.1           0060.7050.28c3        dynamic
  
  194.168.1.11          0001.c7d6.0188        dynamic
  
  196.168.1.12          0050.0f6e.6598        dynamic
  
Вопрос:

Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы? Да, только устройства, на которые отправлялись эхо-запросы.

	Вопрос для повторения
В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях? Увеличение доли широковещательного трафика и рост нежелательной нагрузки на сеть.


