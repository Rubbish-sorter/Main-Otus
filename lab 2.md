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


