# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH
## Топология
 ![lab 5](https://user-images.githubusercontent.com/80044182/117867024-7b6a2f00-b2a0-11eb-8d63-e0824cf6774d.png)
## Таблица адресации

--------
|Устройство |	Интерфейс|	IP-адрес |Маска подсети	|Шлюз по умолчанию |
|-----|-------|------|----|-----|   
|R1     | G0/0/1 |192.168.1.1	|255.255.255.0	|— |  
|S1	| VLAN 1| 192.168.1.11	| 255.255.255.0	| 192.168.1.1|  
|PC-A	|NIC	|192.168.1.3	|255.255.255.0	|192.168.1.1 |  

## Задачи
Часть 1. Настройка основных параметров устройства
Часть 2. Настройка маршрутизатора для доступа по протоколу SSH
Часть 3. Настройка коммутатора для доступа по протоколу SSH
Часть 4. SSH через интерфейс командной строки (CLI) коммутатора


## Необходимые ресурсы
- 1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)  
- 1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)  
- 1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)  
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.  
- Кабели Ethernet, расположенные в соответствии с топологией  
- 
### Часть 1. Настройка основных параметров устройств

#### Шаг 1. Создайте сеть согласно топологии.
#### Шаг 2. Выполните инициализацию и перезагрузку маршрутизатора и коммутатора.
#### Шаг 3. Настройте маршрутизатор.
Откройте окно конфигурации
a. Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.  
b. Войдите в режим конфигурации.  
c. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
d. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
e. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
f. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.  
g. Зашифруйте открытые пароли.  
h. Создайте баннер, который предупреждает о запрете несанкционированного доступа.  
i. Настройте и активируйте на маршрутизаторе интерфейс G0/0/1, используя информацию, приведенную в таблице адресации.  
j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.  

Router>en  
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#ser pass  
Router(config)#no ip domain-lookup  
Router(config)#enable secret class  
Router(config)#line con 0  
Router(config-line)#pass cisco  
Router(config-line)#logging synchronous  
Router(config-line)#login  
Router(config-line)#end  
Router#  
%SYS-5-CONFIG_I: Configured from console by console  

Router(config)#line vty 0 4  
Router(config-line)#pass cisco  
Router(config-line)#login  
Router(config-line)#exit  
Router(config)#banner motd #Unauthorized access is strictly prohibited!#  
Router(config)#int g0/0/1  
Router(config-if)#ip addr 192.168.1.1 255.255.255.0  
Router(config-if)#no sh  

Router(config-if)#  
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up  

Router(config-if)#exit  
Router#wr  
Building configuration...  
[OK]  

#### Шаг 4. Настройте компьютер PC-A.  
a. Настройте для PC-A IP-адрес и маску подсети. 
b. Настройте для PC-A шлюз по умолчанию.  
#### Шаг 5. Проверьте подключение к сети.  
Пошлите с PC-A команду Ping на маршрутизатор R1. Если эхо-запрос с помощью команды ping не проходит, найдите и устраните неполадки подключения.  
C:\>ping 192.168.1.1  

Pinging 192.168.1.1 with 32 bytes of data:  

Reply from 192.168.1.1: bytes=32 time=1ms TTL=255  
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255  
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255  
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255  
 
Ping statistics for 192.168.1.1:  
Packets: Sent = 4, Received = 4,   
Lost = 0 (0% loss),  
Approximate round trip times in milli-seconds:   
Minimum = 0ms, Maximum = 1ms, Average = 0ms  

### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH
#### Шаг 1. Настройте аутентификацию устройств.
Откройте окно конфигурации  
a. Задайте имя устройства.   
b. Задайте домен для устройства.  
#### Шаг 2. Создайте ключ шифрования с указанием его длины.  
#### Шаг 3. Создайте имя пользователя в локальной базе учетных записей.  
Настройте имя пользователя, используя admin в качестве имени пользователя и Adm1nP@55 в качестве пароля.  
#### Шаг 4. Активируйте протокол SSH на линиях VTY.  
a.	Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды transport input.  
b.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей. 
#### Шаг 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Router#conf t  
Enter configuration commands, one per line. End with CNTL/Z.  
Router(config)#hostname R1  
R1(config)#ip domain name test.com   
R1(config)#crypto key generate rsa  
The name for the keys will be: R1.test.com  
Choose the size of the key modulus in the range of 360 to 2048 for your  
General Purpose Keys. Choosing a key modulus greater than 512 may take a few minutes.  
How many bits in the modulus [512]: 1024  
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]  

R1(config)# username admin privilege 15 secret Adm1nP@55  
*Mar 1 0:43:28.610: %SSH-5-ENABLED: SSH 1.99 has been enabled  
R1(config-line)#transport input ssh   
R1(config-line)#login local    
R1(config-line)#^Z  
R1#  
%SYS-5-CONFIG_I: Configured from console by console 
R1#wr  
Building configuration...  
[OK]  

#### Шаг 6. Установите соединение с маршрутизатором по протоколу SSH.
a.	Запустите Tera Term с PC-A.
b.	Установите SSH-подключение к R1. Use the username admin and password Adm1nP@55. У вас должно получиться установить SSH-подключение к R1

Password: Adm1nP@55  

Unauthorized access is strictly prohibited!  

R1#  

### Часть 3. Настройка коммутатора для доступа по протоколу SSH  
#### Шаг 1. Настройте основные параметры коммутатора.  
Откройте окно конфигурации  
a.	Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.  
b.	Войдите в режим конфигурации.   
c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.  
d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.  
e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.  
f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.  
g.	Зашифруйте открытые пароли.  
h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.  
i.	Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.  
j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.  

Switch>en   
Switch#conf t   
Enter configuration commands, one per line. End with CNTL/Z.   
Switch(config)#no ip domain-lookup   
Switch(config)#ser pass   
Switch(config)#enable secret class   
Switch(config)#line con 0   
Switch(config-line)#pass cisco   
Switch(config-line)#login   
Switch(config-line)#line vty 0 4   
Switch(config-line)#pass cisco   
Switch(config-line)#login   
Switch(config-line)#exit   
Switch(config)#banner motd #Unauthorized access is strictly prohibited!#   
Switch(config)#int vlan 1     
Switch(config-if)#ip addr 192.168.1.11 255.255.255.0     
Switch(config-if)#ip default-gateway 192.168.1.1    
Switch(config-if)#no sh    
Switch(config-if)#    
%LINK-5-CHANGED: Interface Vlan1, changed state to up    

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up   
Switch(config-if)#do wr    
Building configuration...    
[OK]     
Switch(config-if)#exit    
#### Шаг 2. Настройте коммутатор для соединения по протоколу SSH. 
Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.  
a.	Настройте имя устройства, как указано в таблице адресации.   
b.	Задайте домен для устройства.   
c.	Создайте ключ шифрования с указанием его длины.   
d.	Создайте имя пользователя в локальной базе учетных записей.  
e.	Активируйте протоколы Telnet и SSH на линиях VTY.  
f.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.    
Switch(config)#hostname S1   
S1(config)#ip domain name test.com   
S1(config)#cr key gen rsa  
The name for the keys will be: S1.test.com  
Choose the size of the key modulus in the range of 360 to 2048 for your  
General Purpose Keys. Choosing a key modulus greater than 512 may take  
a few minutes.  

How many bits in the modulus [512]: 1024  
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]  
S1(config)#username admin privilege 15 secret gasout  
S1(config)#line vty 0 4  
S1(config-line)#tr input all  
S1(config-line)#login local   
S1(config-line)#^Z  
S1#  
%SYS-5-CONFIG_I: Configured from console by console   

#### Шаг 3. Установите соединение с коммутатором по протоколу SSH.
Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1.  
Вопрос: 
Удалось ли вам установить SSH-соединение с коммутатором?  

Password: gasout  

Unauthorized access is strictly prohibited!  

S1#  


### Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора  
#### Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS.  
S1# ssh?   
  -c Select encryption algorithm  
  -l Log in using this user name  
  -m Select HMAC algorithm  
  -o Specify options  
  -p Connect to this port  
  -v Specify SSH Protocol Version   
  -vrf Specify vrf name  
    WORD IP-адрес или имя хоста удаленной системы  
    **Разбор отдельных параметров:** -с - алгоритм шифрования, -l - задать имя пользователя, -v - версия 1 (устаревшая) или 2, -p специфический порт, остальное используется слишком редко.  
#### Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.    
a.	Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду –l admin. Это позволит вам войти в систему под именем admin. При появлении приглашения введите в качестве пароля Adm1nP@55   
b.	Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш Ctrl+Shift+6. Отпустите клавиши Ctrl+Shift+6 и нажмите x. Отображается приглашение привилегированного режима EXEC коммутатора.  
c.	Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.  
d.	Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду exit.  
S1#  
S1#ssh -l admin 192.168.1.1  
Password: Adm1nP@55  

Unauthorized access is strictly prohibited!  

R1>  
R1>exit  

[Connection to 192.168.1.1 closed by foreign host]    
S1#    

Переключение не получилось в Packet Tracer.    
**Вопрос:**   
Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки?  
**Ответ:**  
Точно 1.3, 1.5, 2.0.  















