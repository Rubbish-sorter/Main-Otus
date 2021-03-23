# Лабораторная работа. Базовая настройка коммутатора
##  Топология
![lab1](https://user-images.githubusercontent.com/80044182/112204322-93b8b880-8c24-11eb-81d5-d9aa70846360.png)
### Таблица адресации
| Устройство  |  Интерфейс | IP-адрес/префикс  | 
| ------------ | ------------ | ------------ |
| S1| VLAN 1   | 192.168.1.2 /24 |  
|  PC-A | NIC  | 192.168.1.10 /24  | 
## 	Задачи
### Часть 1. Проверка конфигурации коммутатора по умолчанию
### Часть 2. Создание сети и настройка основных параметров устройства
- Настройте базовые параметры коммутатора.
- 	Настройте IP-адрес для ПК.
- Отобразите конфигурацию устройства.
- 	Протестируйте сквозное соединение, отправив эхо-запрос.
- Протестируйте возможности удаленного управления с помощью Telnet.
### Часть 3. Проверка сетевых подключений
- Настройте базовые параметры коммутатора.
- Настройте IP-адрес для ПК.
- Отобразите конфигурацию устройства.
- Протестируйте сквозное соединение, отправив эхо-запрос.
- Протестируйте возможности удаленного управления с помощью Telnet.
## Необходимые ресурсы
-	1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
-	1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
- 1 консольный кабель для настройки устройства на базе Cisco IOS через консольный порт.
-	1 кабель Ethernet, как показано в топологии.
### Создайте сеть согласно топологии.
  a.	Подсоедините консольный кабель, как показано в топологии. На данном этапе не подключайте кабель Ethernet компьютера PC-A.
  b.	Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.
**Вопрос:**
Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?
**Ответ:** эти сервисы не настроены.
## Шаг 1. Проверьте настройки коммутатора по умолчанию.
Предположим, что коммутатор не имеет файла конфигурации, сохраненного в энергонезависимой памяти (NVRAM). Консольное подключение к коммутатору с помощью Tera Term или другой программы эмуляции терминала предоставит доступ к командной строке пользовательского режима EXEC в виде Switch>. Введите команду enable, чтобы войти в привилегированный режим EXEC.

Switch>

Switch>enable

Switch#

Switch# show running-config

Building configuration...

....

end

b.	Изучите текущий файл running configuration.

**Вопросы:**

1. Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?
2. Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960?
3. 3. Каков диапазон значений, отображаемых в vty-линиях?

**Ответы:**

1. 24
2. 2 uplink
3. 0-4 и 5-15
c.  Изучите файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM).
Вопрос:
Почему появляется это сообщение?

 Switch#show startup-config

startup-config is not present

**Ответ:** настройки не менялись.

d.	Изучите характеристики SVI для VLAN 1.
Switch#sh int vlan1
Vlan1 is administratively down, line protocol is down
  Hardware is CPU Interface, address is 0002.1732.8919 (bia 0002.1732.8919)
  MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec, reliability 255/255, txload 1/255, rxload 1/255
  
  Encapsulation ARPA, loopback not set
  
  ARP type: ARPA, ARP Timeout 04:00:00
  
     ....
     
**Вопросы:**
1.  Назначен ли IP-адрес сети VLAN 1?
2.  Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.
Данный интерфейс включен? 
**Ответы:**
1. нет
2. dot 0002.1732.8919 и интерфейс выключен 
e.	Изучите IP-свойства интерфейса SVI сети VLAN 1.

**Вопрос:**

Какие выходные данные вы видите? 

**Ответ:** MTU - максимальный размер пакета, установленная пропусканая способность, задержка припередаче пакетов в микросекундах, надёжность - индикатор проблем на физическом уровне, загрузка интерфейса относительно номинала, тип инкапсуляции, параметры работы протокола ARP и далее статистика вырезана.
f.	Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК.

**Вопрос:**

Какие выходные данные вы видите?

%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/6, changed state to up
 
Интерфейс и протокол канального уровня запущены.
g.	Изучите сведения о версии ОС Cisco IOS на коммутаторе.

**Вопросы:**

1.  Под управлением какой версии ОС Cisco IOS работает коммутатор?
2.  Как называется файл образа системы?
3.  Какой базовый MAC-адрес назначен коммутатору?

**Ответы:**

Данные взяты из лога загрузки коммутатора, здесь не приводится.
1. 15.0(2)SE4, 15.0
2. 2960-lanbasek9-mz.150-2.SE4.bin
3. 00:17:59:A7:51:80
h.	Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A.
Switch# show interface f0/6

FastEthernet0/6 is up, line protocol is up (connected)

  Hardware is Lance, address is 0003.e4d4.a106 (bia 0003.e4d4.a106)
 BW 100000 Kbit, DLY 1000 usec, reliability 255/255, txload 1/255, rxload 1/255
 
  Encapsulation ARPA, loopback not set
  
  Keepalive set (10 sec)
  
  Full-duplex, 100Mb/s
  
  input flow-control is off, output flow-control is off
  
  ARP type: ARPA, ARP Timeout 04:00:00
  
**Вопросы:**

1.  Интерфейс включен или выключен?
2.  Что нужно сделать, чтобы включить интерфейс?
3.  Какой MAC-адрес у интерфейса?
4.  Какие настройки скорости и дуплекса заданы в интерфейсе?

**Ответы:**

1.  интерфейс включен
2.  dot 0003.e4d4.a106
3.  100 Мбит/сек, полный дуплекс
i.	Изучите параметры сети VLAN по умолчанию на коммутаторе.
Switch#show vlan brief

VLAN Name                             Status    Ports

---- -------------------------------- --------- -------------------------------

1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4

                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                
                                                Gig0/1, Gig0/2
                                                
1002 fddi-default                     active   

1003 token-ring-default               active    

1004 fddinet-default                  active   

1005 trnet-default                    active 

**Вопросы**

1.  Какое имя присвоено сети VLAN 1 по умолчанию?
2.  Какие порты расположены в сети VLAN 1?
3.  Активна ли сеть VLAN 1?
4. К какому типу сетей VLAN принадлежит VLAN по умолчанию?

 **Ответы:**
 
 1. default
 2. Все порты Ethernet.
 3. Да, сеть активна.
 4. Виртуальная, логическая сеть.
 j.	Изучите флеш-память.
Выполните одну из следующих команд, чтобы изучить содержимое флеш-каталога.

Switch# show flash 

Directory of flash:/

1  -rw-     4670455          <no date>  2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)

В конце имени файла указано расширение, например .bin. Каталоги не имеют расширения файла.

**Вопрос:**

Какое имя присвоено образу Cisco IOS?

**Ответ:** 2960-lanbasek9-mz.150-2.SE4.bin

### Часть 2. Настройка базовых параметров сетевых устройств
Во второй части необходимо будет настроить основные параметры коммутатора и компьютера.
#### Шаг 1. Настройте базовые параметры коммутатора.
a.	В режиме глобальной конфигурации скопируйте следующие базовые параметры конфигурации и вставьте их в файл на коммутаторе S1. 
no ip domain-lookup
hostname S1
service password-encryption
enable secret class
banner motd 
Unauthorized access is strictly prohibited. 

Switch#configure terminal

Enter configuration commands, one per line.  End with CNTL/Z

Switch(config)#no ip domain-lookup

Switch(config)#hostname S1

S1(config)#service password-encryption

S1(config)#enable secret class

S1(config)#banner motd # Unauthorized access is strictly prohibited. #

b.	Назначьте IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому вы получите возможность удаленного управления коммутатором.

S1(config)#interface vlan 1
	
S1(config-if)#ip address 192.168.1.2 255.255.255.0

S1(config-if)#ip default-gateway 192.168.1.1

S1(config-if)# no shutdown

S1(config-if)#

%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

S1(config-if)#^Z

S1#

%SYS-5-CONFIG_I: Configured from console by console

c.	Доступ через порт консоли также следует ограничить  с помощью пароля. Используйте cisco в качестве пароля для входа в консоль в этом задании. Конфигурация по умолчанию разрешает все консольные подключения без пароля. Чтобы консольные сообщения не прерывали выполнение команд, используйте параметр logging synchronous.

S1(config)#line con 0

S1(config-line)#logging synchronous

S1(config-line)#password cisco

S1(config-line)# login

S1(config-line)#^Z

S1#

%SYS-5-CONFIG_I: Configured from console by console

d.	Настройте каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.
Вопрос:
Для чего нужна команда login?

**Ответ:** для запроса пароля при входе.

S1(config)#line vty 0 15

S1(config-line)#password cisco

S1(config-line)#login

S1(config-line)#end

S1#

%SYS-5-CONFIG_I: Configured from console by console

#### Шаг 2. Настройте IP-адрес на компьютере PC-A.
Назначьте компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации.

C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   
   Link-local IPv6 Address.........: FE80::260:3EFF:FE04:D0E5
   
   IPv6 Address....................: ::
   
   IPv4 Address....................: 192.168.1.10
   
   Subnet Mask.....................: 255.255.255.0
   
   Default Gateway.................: ::
   
                                     192.168.1.1


S1#ping 192.168.1.10

Type escape sequence to abort.

Sending 5, 100-byte ICMP Echos to 192.168.1.10, timeout is 2 seconds:

.!!!!

Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms

### Часть 3. Проверка сетевых подключений
В третьей части лабораторной работы вам предстоит проверить и задокументировать конфигурацию коммутатора, протестировать сквозное соединение между компьютером PC-A и коммутатором S1, а также протестировать возможность удаленного управления коммутатором.
#### Шаг 1. Отобразите конфигурацию коммутатора.

S1#show running-config

Building configuration...

Current configuration : 1318 bytes

!

version 15.0

no service timestamps log datetime msec

no service timestamps debug datetime msec

service password-encryption

!

hostname S1

!

enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1

!

!

!

no ip domain-lookup
!

!

!

spanning-tree mode pvst

spanning-tree extend system-id

!

........

!

interface Vlan1

 ip address 192.168.1.2 255.255.255.0
 
!

banner motd ^C Unauthorized access is strictly prohibited. ^C

!

!

!

line con 0

 password 7 0822455D0A16
 
 logging synchronous
 
 login
 
!

line vty 0 4

 password 7 0822455D0A16
 
 login
 
line vty 5 15

 password 7 0822455D0A16
 
 login
 
!

!

!

!

end

b.	Проверьте параметры VLAN 1.

S1# show interface vlan 1 

Vlan1 is up, line protocol is up

  Hardware is CPU Interface, address is 0002.1732.8919 (bia 0002.1732.8919)
  
  Internet address is 192.168.1.2/24
  
  MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
     
  Encapsulation ARPA, loopback not set
  
  ARP type: ARPA, ARP Timeout 04:00:00

.......
**вопросы:**
1) Какова полоса пропускания этого интерфейса?
2) В каком состоянии находится VLAN 1?
3) В каком состоянии находится канальный протокол?
**Ответы:**
1) 100МБит/сек
2) активен поскольку был включен принудительно
3) активен

a.	В командной строке компьютера PC-A с помощью утилиты ping проверьте связь сначала с адресом PC-A.
C:\> ping 192.168.1.10

Reply from 192.168.1.10: bytes=32 time=2ms TTL=128

Reply from 192.168.1.10: bytes=32 time=5ms TTL=128

Reply from 192.168.1.10: bytes=32 time=5ms TTL=128

Reply from 192.168.1.10: bytes=32 time<1ms TTL=128

b.	Из командной строки компьютера PC-A отправьте эхо-запрос на административный адрес интерфейса SVI коммутатора S1.
C:\> ping 192.168.1.2
Reply from 192.168.1.2: bytes=32 time=4ms TTL=255

Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

#### Шаг 3. Проверьте удаленное управление коммутатором S1.
После этого используйте удаленный доступ к устройству с помощью Telnet. В этой лабораторной работе устройства PC-A и S1 расположены рядом. В производственной сети коммутатор может находиться в коммутационном шкафу на последнем этаже, в то время как административный компьютер находится на первом этаже. На данном этапе вам предстоит использовать Telnet для удаленного доступа к коммутатору S1 через его административный адрес SVI. Telnet — это не безопасный протокол, но вы можете использовать его для проверки удаленного доступа. В случае с Telnet вся информация, включая пароли и команды, отправляется через сеанс в незашифрованном виде. В последующих лабораторных работах вы будете использовать протокол SSH для удаленного доступа к сетевым устройствам.
a.	Откройте Tera Term или другую программу эмуляции терминала с возможностью Telnet. 
b.	Выберите сервер Telnet и укажите адрес управления SVI для подключения к S1.  Пароль: cisco.
c.	После ввода пароля cisco вы окажетесь в командной строке пользовательского режима. Для перехода в исполнительский режим EXEC введите команду enable и используйте секретный пароль class.
d.	Сохраните конфигурацию.
e.	Чтобы завершить сеанс Telnet, введите exit.

Trying 192.168.1.2 ...Open Unauthorized access is strictly prohibited. 

User Access Verification

Password: cisco

S1>enable

Password: class

S1#

S1#write memory

Building configuration...

[OK]

S1# exit

## Вопросы для повторения
1.	Зачем необходимо настраивать пароль VTY для коммутатора?
2.	Что нужно сделать, чтобы пароли не отправлялись в незашифрованном виде?
**Ответы:**
1. Для использования удалённого подключения, иначе это невозможно.
2. Использовать зашифрованные пароли (secret) и сервис шифрования паролей, использовать SSH подключения.

# Приложение А. Инициализация и перезагрузка коммутатора
a.	Подключитесь к коммутатору с помощью консоли и войдите в привилегированный режим EXEC.
b.	Воспользуйтесь командой show flash, чтобы определить, были ли созданы сети VLAN на коммутаторе.

Press RETURN to get started!

 Unauthorized access is strictly prohibited. 

User Access Verification

Password: cisco

S1>enable

Password: class

S1#show flash

Directory of flash:/

1  -rw-     4670455          <no date>  2960-lanbasek9-mz.150-2.SE4.bin
5  -rw-        1318          <no date>  config.text

64016384 bytes total (59344611 bytes free)

c.	Если во флеш-памяти обнаружен файл vlan.dat, удалите его.

Нет, использовался Vlan 1.

d.	Появится запрос о проверке имени файла. Если вы ввели имя правильно, нажмите клавишу Enter. В противном случае вы можете изменить имя файла.
Будет предложено подтвердить удаление этого файла. Нажмите клавишу Enter для подтверждения.

Не применимо.

e.	Введите команду erase startup-config, чтобы удалить файл загрузочной конфигурации из NVRAM. Появится запрос об удалении конфигурационного файла. Нажмите клавишу Enter для подтверждения.

S1#erase startup-config

Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]

Erase of nvram: complete

%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram

f.	Перезагрузите коммутатор, чтобы удалить устаревшую информацию о конфигурации из памяти. Затем появится запрос на подтверждение перезагрузки коммутатора. Нажмите клавишу Enter, чтобы продолжить.

S1#reload

Proceed with reload? [confirm] y

g.	После перезагрузки коммутатора появится запрос о входе в диалоговое окно начальной конфигурации. 

Switch>

