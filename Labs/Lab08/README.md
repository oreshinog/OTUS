# Лабораторная работа - Настройка DHCPv6

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab08/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IPv6-адрес|
|--|--|--|
|R1|G0/0/0|2001:db8:acad:2::1/64|
|||fe80::1|
|R1|G0/0/1|2001:db8:acad:1::1/64|
|||fe80::1|
|R2|G0/0/0|2001:db8:acad:2::2/64|
|||fe80::2|
|R2|G0/0/1|2001:db8:acad:3::1/64|
|||fe80::1|
|PC-A|NIC|DHCP|
|PC-B|NIC|DHCP|

# Задачи

**Часть 1. Создание сети и настройка основных параметров устройства**
**Часть 2. Проверка назначения адреса SLAAC от R1**
**Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1**
**Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1**
**Часть 5. Настройка и проверка DHCPv6 Relay на R2**


## Необходимые ресурсы
**2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)**
**2 коммутатора (Cisco 2960 с образом lanbasek9 Cisco IOS версии 15.2 (2) или аналогичным) - опционально**
**2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)**
**Консольные кабели для настройки устройств Cisco IOS через консольные порты.**
**Кабели Ethernet, расположенные в соответствии с топологией**

## Инструкции

## Часть 1.Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

### Шаг 1.1.Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab08/Img/Img01.png)

### Шаг 1.2.Настройте базовые параметры каждого коммутатора. (необязательно)

1.  Присвойте коммутатору имя устройства.
2.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
4.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
5.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
6.  Зашифруйте открытые пароли.
7.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
8.  Отключите все неиспользуемые порты.
9.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#pass cisco
S1(config-line)#logging synchronous
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

S1(config)#int ra fa0/1-4,fa0/7-24,gi0/1-2
S1(config-if-range)#sh
S1(config-if-range)#ex
S1(config)#ex
S1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#no ip domain lookup
S2(config)#enable secret class
S2(config)#line con 0
S2(config-line)#pass cisco
S2(config-line)#logging synchronous
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#pass cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 5 15
S2(config-line)#pass cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

S2(config)#int ra fa0/1-4,fa0/6-17,fa0/19-24,gi0/1-2
S2(config-if-range)#sh
S2(config-if-range)#ex
S2(config)#ex
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Шаг 1.3.Произведите базовую настройку маршрутизаторов.

1.3.a.Назначьте маршрутизатору имя устройства.
1.3.b.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
1.3.c.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
1.3.d.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
1.3.e.Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
1.3.f.Зашифруйте открытые пароли.
1.3.g.Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
1.3.h.Активация IPv6-маршрутизации
1.3.i.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
Router>enable
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain lookup
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#pass cisco
R1(config-line)#logging synchronous
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 5 15
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

R1(config)#ipv
R1(config)#ipv6 uni
R1(config)#ipv6 unicast-routing 
R1(config)#ex
R1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```
Router>enable
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R2
R2(config)#no ip domain lookup
R2(config)#enable secret class
R2(config)#line con 0
R2(config-line)#pass cisco
R2(config-line)#logging synchronous
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 0 4
R2(config-line)#pass cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 5 15
R2(config-line)#pass cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#service password-encryption
R2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

R2(config)#ipv6 unicast-routing
R2(config)#ex
R2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Шаг 1.4.Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.

1.4.a.Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.

```
R1(config)#int gi0/0/0
R1(config-if)#ipv6 add 2001:db8:acad:2::1/64
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)#no sh
R1(config-if)#ex
R1(config)#int gi0/0/1
R1(config-if)#ipv6 add 2001:db8:acad:1::1/64
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)#no sh
```

```
R2(config)#int gi 0/0/0
R2(config-if)#ipv6 add 2001:db8:acad:2::2/64
R2(config-if)#ipv6 add fe80::2 link-local
R2(config-if)#no sh
R2(config-if)#ex
R2(config)#int gi 0/0/1
R2(config-if)#ipv6 add 2001:db8:acad:3::1/64
R2(config-if)#ipv6 add fe80::1 link-local
R2(config-if)#no sh
```

1.4.b.Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.

```
R1(config)#ipv6 route ::/0 2001:db8:acad:2::2
```

```
R2(config)#ipv6 route ::/0 2001:db8:acad:2::1
```

1.4.c.Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1

```
R1#ping 2001:db8:acad:3::1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

1.4.d.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

```
R2#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

## Часть 2.Проверка назначения адреса SLAAC от R1

В части 2 вы убедитесь, что узел PC-A получает адрес IPv6 с помощью метода SLAAC.

Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6.

![PC-A_ipv6c1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab08/Img/Img02.png)

![PC-A_ipv6c2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab08/Img/Img03.png)

Через несколько минут результаты команды **ipconfig** должны показать, что PC-A присвоил себе адрес из сети 2001:db8:1::/64.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::260:5CFF:FE27:9250
   IPv6 Address....................: 2001:DB8:ACAD:1:260:5CFF:FE27:9250
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
```
#### Вопрос:

Откуда взялась часть адреса с идентификатором хоста? MAC адрес сетевой карты PC-A - 00:60:5C:27:92:50, при преобразовании в формат EUI-64, инвертируется 7-й бит в 1-м байте (00000000 превращается в 00000010, или 0х02) и добавляется FF:FE между 3-м и 4-м байтами, после этого получаем идентификатор вида 0**2**60:5C**FF**:**FE**27:9250

## Часть 1.Настройка и проверка сервера DHCPv6 на R1

В части 3 выполняется настройка и проверка состояния DHCP-сервера на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.

### Шаг 1.1.Более подробно изучите конфигурацию PC-A.

1.1.a.Выполните команду **ipconfig /all** на PC-A и посмотрите на результат.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0060.5C27.9250
   Link-local IPv6 Address.........: FE80::260:5CFF:FE27:9250
   IPv6 Address....................: 2001:DB8:ACAD:1:260:5CFF:FE27:9250
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-48-D0-A8-16-00-60-5C-27-92-50
   DNS Servers.....................: ::
                                     0.0.0.0

```

1.1.b.Обратите внимание, что основной DNS-суффикс отсутствует. Также обратите внимание, что предоставленные адреса DNS-сервера являются адресами «локального сайта anycast», а не одноадресные адреса, как ожидалось.

### Шаг 1.2.Настройте R1 для предоставления DHCPv6 без состояния для PC-A.

1.2.a.Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad::1, а имя домена — как stateless.com.

```
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::1
R1(config-dhcpv6)#domain-name stateless.com
```

1.2.b.Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.

```
R1(config)#int gi0/0/1
R1(config-if)#ipv6 nd other-config-flag
R1(config-if)#ipv6 dhcp server R1-STATELESS
```

1.2.c.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

1.2.d.Перезапустите PC-A.
1.2.e.Проверьте вывод **ipconfig /all** и обратите внимание на изменения.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: stateless.com 
   Physical Address................: 0060.5C27.9250
   Link-local IPv6 Address.........: FE80::260:5CFF:FE27:9250
   IPv6 Address....................: 2001:DB8:ACAD:1:260:5CFF:FE27:9250
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 275810774
   DHCPv6 Client DUID..............: 00-01-00-01-48-D0-A8-16-00-60-5C-27-92-50
   DNS Servers.....................: 2001:DB8:ACAD::1
                                     0.0.0.0
```

1.2.f.Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2.

```
C:\>ping 2001:db8:acad:3::1

Pinging 2001:db8:acad:3::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

Ping statistics for 2001:DB8:ACAD:3::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

## Часть 2.Настройка сервера DHCPv6 с сохранением состояния на R1

В части 4 настраивается R1 для ответа на запросы DHCPv6 от локальной сети на R2.

2.1.a.Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad::254 и задайте доменное имя STATEFUL.com.

```
R1(config)#ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#add pref 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATEFUL.com
```

2.1.b.Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1.

```
R1(config)#int gi0/0/0
R1(config-if)#ipv6 dhcp server R2-STATEFUL
```

## Часть 3.Настройка и проверка ретрансляции DHCPv6 на R2.

В части 5 необходимо настроить и проверить ретрансляцию DHCPv6 на R2, позволяя PC-B получать адрес IPv6.

### Шаг 3.1.Включите PC-B и проверьте адрес SLAAC, который он генерирует.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0003.E44B.05B1
   Link-local IPv6 Address.........: FE80::203:E4FF:FE4B:5B1
   IPv6 Address....................: 2001:DB8:ACAD:3:203:E4FF:FE4B:5B1
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-76-90-53-10-00-03-E4-4B-05-B1
   DNS Servers.....................: ::
                                     0.0.0.0
```

Обратите внимание на вывод, что используется префикс 2001:db8:acad:3::

### Шаг 3.2.Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1.

3.2.a.Настройте команду **ipv6 dhcp relay** на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1. Также настройте команду **managed-config-flag** .

***CPT не поддерживает ipv6 dhcp relay, настроил локально на R2***

```
R2(config)#ipv6 dhcp pool R2-STATEFUL
R2(config-dhcpv6)#dns-server 2001:db8:acad::254
R2(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R2(config-dhcpv6)#domain-name STATEFUL.com


```

### Шаг 3.3.Попытка получить адрес IPv6 из DHCPv6 на PC-B.

3.3.a.Перезапустите PC-B.

3.3.b.Откройте командную строку на PC-B и выполните команду **ipconfig /all** и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: STATEFUL.com 
   Physical Address................: 0003.E44B.05B1
   Link-local IPv6 Address.........: FE80::203:E4FF:FE4B:5B1
   IPv6 Address....................: 2001:DB8:ACAD:3:AAA:1839:FC05:EE73
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 27615200
   DHCPv6 Client DUID..............: 00-01-00-01-76-90-53-10-00-03-E4-4B-05-B1
   DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0
```

3.3.c.Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

```
C:\>ping 2001:db8:acad:1::1

Pinging 2001:db8:acad:1::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254

Ping statistics for 2001:DB8:ACAD:1::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

# Настройка DHCPv4

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab08/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IP-адрес|Маска подсети|Шлюз по-умолчанию|
|--|--|--|--|--|
|R1|G0/0/0|10.0.0.1|255.255.255.252||
||G0/0/1||||
||G0/0/1.100|192.168.1.1|255.255.255.192||
||G0/0/1.200|192.168.1.65|255.255.255.224||
||G0/0/1.1000||||
|R2|G0/0/0|10.0.0.2|255.255.255.252||
|R2|G0/0/1|192.168.1.97|255.255.255.240||
|S1|VLAN200|192.168.1.66|255.255.255.224|192.168.1.65|
|S2|VLAN1|192.168.1.98|255.255.255.240|192.168.1.97|
|PC-A|NIC|DHCP|DHCP|DHCP|
|PC-B|NIC|DHCP|DHCP|DHCP|

## Таблица VLAN
|VLAN|Имя|Назначенный интерфейс|
|--|--|--|
|1|Нет|S2: F0/18|
|100|Клиенты|S1: F0/6|
|200|Управление|S1: VLAN 200|
|999|Parking_Lot|S1: F0/1-4,F0/7-24,G0/1-2|
|1000|Собственная||


# Задачи

**Часть 1. Создание сети и настройка основных параметров устройства**
**Часть 2. Настройка и проверка двух серверов DHCPv4 на R1**
**Часть 3. Настройка и проверка DHCP-ретрансляции на R2**

## Необходимые ресурсы
   2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
   2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
   2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
   Консольные кабели для настройки устройств Cisco IOS через консольные порты.
   Кабели Ethernet, расположенные в соответствии с топологией
    
## Инструкции
    
## Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

### Создание схемы адресации

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:
1.  Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).

|Адрес подсети|Маска подсети|Диапазон адресов|
|--|--|--|
|192.168.1.0|255.255.255.192|192.168.1.1-192.168.1.62|

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100  . - адрес **192.168.1.1**

2.  Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1).
    
|Адрес подсети|Маска подсети|Диапазон адресов|
|--|--|--|
|192.168.1.64|255.255.255.224|192.168.1.65-192.168.1.94|

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.
1-й адрес - **192.168.1.65**
2-й адрес - **192.168.1.66**

3.  Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
    
|Адрес подсети|Маска подсети|Диапазон адресов|
|--|--|--|
|192.168.1.96|255.255.255.240|192.168.1.97-192.168.1.110|

Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.  - адрес **192.168.1.97**
S2 - адрес **192.168.1.98**

### Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab08/Img/Img01.png)

### Произведите базовую настройку маршрутизаторов.
    
 1.  Назначьте маршрутизатору имя устройства.
2.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
4.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
5.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
6.  Зашифруйте открытые пароли.
7.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
8.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
9.  Установите часы на маршрутизаторе на сегодняшнее время и дату.

```
Router>enable
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain lookup
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#pass cisco
R1(config-line)#logging synchronous
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 5 15
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption
R1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

R1(config)#clock timezone NSB 7
R1(config)#ex
R1#clock set 13:40:00 12 JAN 2025

R1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```
Router>enable
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R2
R2(config)#no ip domain lookup
R2(config)#enable secret class
R2(config)#line con 0
R2(config-line)#pass cisco
R2(config-line)#logging synchronous
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 0 4
R2(config-line)#pass cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 5 15
R2(config-line)#pass cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#service password-encryption
R2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

R2(config)#clock timezone NSB 7
R2(config)#ex
R2#clock set 13:41:00 12 JAN 2025
R2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Настройка маршрутизации между сетями VLAN на маршрутизаторе R1
   
   1.  Активируйте интерфейс G0/0/1 на маршрутизаторе.
   2.  Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
R1(config)#int g0/0/1
R1(config-if)#no sh

R1(config)#int g0/0/1.100
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.100, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.100, changed state to up

R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#des Clients
R1(config-subif)#ip add 192.168.1.1 255.255.255.192
R1(config-subif)#ex
R1(config)#int g0/0/1.200
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.200, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.200, changed state to up

R1(config-subif)#enc dot1Q 200
R1(config-subif)#des Management
R1(config-subif)#ip add 192.168.1.65 255.255.255.240
R1(config-subif)#ex
R1(config)#int g0/0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up

R1(config-subif)#enc dot1Q 1000 native
R1(config-subif)#des Own
R1(config-subif)#ex
```

3.  Убедитесь, что вспомогательные интерфейсы работают.

```
R1#sh int g0/0/1.100
GigabitEthernet0/0/1.100 is up, line protocol is up (connected)
  Hardware is PQUICC_FEC, address is 0001.4293.a202 (bia 0001.4293.a202)
  Internet address is 192.168.1.1/26
  MTU 1500 bytes, BW 100000 Kbit, DLY 100 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation 802.1Q Virtual LAN, Vlan ID 100
  ARP type: ARPA, ARP Timeout 04:00:00, 
  Last clearing of "show interface" counters never

R1#sh int g0/0/1.200
GigabitEthernet0/0/1.200 is up, line protocol is up (connected)
  Hardware is PQUICC_FEC, address is 0001.4293.a202 (bia 0001.4293.a202)
  Internet address is 192.168.1.65/28
  MTU 1500 bytes, BW 100000 Kbit, DLY 100 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation 802.1Q Virtual LAN, Vlan ID 200
  ARP type: ARPA, ARP Timeout 04:00:00, 
  Last clearing of "show interface" counters never

R1#sh int g0/0/1.1000
GigabitEthernet0/0/1.1000 is up, line protocol is up (connected)
  Hardware is PQUICC_FEC, address is 0001.4293.a202 (bia 0001.4293.a202)
  MTU 1500 bytes, BW 100000 Kbit, DLY 100 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation 802.1Q Virtual LAN, Vlan ID 1000
  ARP type: ARPA, ARP Timeout 04:00:00, 
  Last clearing of "show interface" counters never
```
        
### Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов
    
1.  Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.

```
R2(config)#int g0/0/1
R2(config-if)#ip add 192.168.1.97 255.255.255.240
```

2.  Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.

```
R1(config)#int g0/0/0
R1(config-if)#ip add 10.0.0.1 255.255.255.252
```

```
R2(config)#int g0/0/0
R2(config-if)#ip add 10.0.0.2 255.255.255.252
```
        
3.  Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.

```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

```
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
        
4.  Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.

```
R1#ping 192.168.1.97

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```
        
5.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```
R2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```
        
### Настройте базовые параметры каждого коммутатора.
    
1.  Присвойте коммутатору имя устройства.
2.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
4.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
5.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
6.  Зашифруйте открытые пароли.
7.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
8.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
9.  Установите часы на маршрутизаторе на сегодняшнее время и дату.

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#pass cisco
S1(config-line)#logging synchronous
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

S1(config)#clock timezone NSB 7
S1(config)#ex
S1#clock set 07:21:00 12 JAN 2025
S1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#no ip domain lookup
S2(config)#enable secret class
S2(config)#line con 0
S2(config-line)#pass cisco
S2(config-line)#logging synchronous
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#pass cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 5 15
S2(config-line)#pass cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)#banner motd #
Enter TEXT message.  End with the character '#'.
Unauthorized access is strictly prohibited. #

S2(config)#clock timezone NSB 7
S2(config)#ex
S2#clock set 07:23:00 12 JAN 2025
S2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Создайте сети VLAN на коммутаторе S1.

Примечание. S2 настроен только с базовыми настройками.

1.  Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
2.  Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
3.  Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2
4.  Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

```
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#ex
S1(config)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#ex
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#ex
S1(config)#vlan 1000
S1(config-vlan)#name Own
S1(config-vlan)#ex
S1(config)#int vlan 200
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan200, changed state to up

S1(config-if)#ip add 192.168.1.66 255.255.255.224
S1(config-if)#ex
S1(config)#ip default-gateway 192.168.1.65
S1(config)#int ra f0/1-4,f0/7-24,g0/1-2
S1(config-if-range)#swi mode acc
S1(config-if-range)#swi acc vlan 999
S1(config-if-range)#sh
```

```
S2(config)#int vlan 1
S2(config-if)#ip add 192.168.1.98 255.255.255.240
S2(config-if)#ex
S2(config)#ip default-gateway 192.168.1.97
S2(config)#int ra f0/1-4,f0/6-17,f0/19-24,g0/1-2
S2(config-if-range)#sh
```

### Назначьте сети VLAN соответствующим интерфейсам коммутатора.
    
1.  Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

```
S1(config)#int f0/6
S1(config-if)#swi mode acc
S1(config-if)#swi acc vlan 100
```        

1.  Убедитесь, что VLAN назначены на правильные интерфейсы.

```
 S1#sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5
100  Clients                          active    Fa0/6
200  Management                       active    
999  Parking_Lot                      active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1000 Own                              active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active     
```

#### 4.Вопрос:

Почему интерфейс F0/5 указан в VLAN 1?
**Потому что любой не настроенный порт по-умолчанию попадает в VLAN 1***

### Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.
    
 1.  Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.

```
S1(config)#int f0/5
S1(config-if)#swi mode tru
```
        
2.  В рамках конфигурации транка установите для native VLAN значение 1000.

```
S1(config-if)#swi tru native vlan 1000
```
        
3.  В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.

```
S1(config-if)#swi tru allowed vlan 100,200,1000
```
        
4.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```
        
5.  Проверьте состояние транка.

```
S1#sh int f0/5 swi
Name: Fa0/5
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1000 (Own)
Voice VLAN: none
Administrative private-vlan host-association: none
Administrative private-vlan mapping: none
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk private VLANs: none
Operational private-vlan: none
Trunking VLANs Enabled: 100,200,1000
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none
```        

#### 5.Вопрос:

Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?
**адрес из DHCP пула, соответствующего сети, из которой пришёл запрос от ПК**

## Настройка и проверка двух серверов DHCPv4 на R1
    
В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.

### Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A
    
1.  Исключите первые пять используемых адресов из каждого пула адресов.
1.  Создайте пул DHCP (используйте уникальное имя для каждого пула).
2.  Укажите сеть, поддерживающую этот DHCP-сервер.
3.  В качестве имени домена укажите CCNA-lab.com.
4.  Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.
5.  Настройте время аренды на 2 дня 12 часов и 30 минут.

```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp excluded-address 192.168.1.65 192.168.1.69

R1(config)#ip dhcp pool DHCP_Clients
R1(dhcp-config)#net 192.168.1.0 255.255.255.192
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#ex
R1(config)#ip dhcp pool DHCP_Management
R1(dhcp-config)#net 192.168.1.64 255.255.255.224
R1(dhcp-config)#default-router 192.168.1.65
R1(dhcp-config)#domain-name CCNA-lab.com
```

***Не поддерживается в CPT?***
```
R1(dhcp-config)#lease 2 12 30
```


6.  Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

```
R2(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
R2(config)#ip dhcp pool R2_Client_LAN
R2(dhcp-config)#net 192.168.1.96 255.255.255.240
R2(dhcp-config)#domain-name CCNA-lab.com
R2(dhcp-config)#default-router 192.168.1.97
```

***Не поддерживается в CPT?***
```
R2(dhcp-config)#lease 2 12 30
```

### Сохраните конфигурацию.

```
R2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```    

### Проверка конфигурации сервера DHCPv4
    
1.  Чтобы просмотреть сведения о пуле, выполните команду **show ip dhcp pool** .

```
R1#sh ip dhcp pool

Pool DHCP_Clients :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 1
 Excluded addresses             : 2
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.1          192.168.1.1      - 192.168.1.62      1    / 2     / 62

Pool DHCP_Management :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 30
 Leased addresses               : 0
 Excluded addresses             : 2
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.65         192.168.1.65     - 192.168.1.94      0    / 2     / 30
```

```
R2#sh ip dhcp pool

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 1
 Excluded addresses             : 1
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.97         192.168.1.97     - 192.168.1.110     1    / 1     / 14
```
        
2.  Выполните команду **show ip dhcp bindings** для проверки установленных назначений адресов DHCP.

```
R1#sh ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.1.6      0060.5C27.9250           --                     Automatic
```

```
R2#sh ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.1.102    0003.E44B.05B1           --                     Automatic
```
        
3.  Выполните команду **show ip dhcp server statistics** для проверки сообщений DHCP.

** Не поддерживается в CPT **
```
R1#sh ip dhcp ?
  binding   DHCP address bindings
  conflict  DHCP address conflicts
  pool      DHCP pools information
  relay     Miscellaneous DHCP relay information
```
      
### Попытка получить IP-адрес от DHCP на PC-A
    
1.  Из командной строки компьютера PC-A выполните команду **ipconfig /all**.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0060.5C27.9250
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.6
   Subnet Mask.....................: 255.255.255.192
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 192.168.1.1
   DHCPv6 IAID.....................: 1831829982
   DHCPv6 Client DUID..............: 00-01-00-01-48-D0-A8-16-00-60-5C-27-92-50
   DNS Servers.....................: ::
                                     0.0.0.0
```
        
2.  После завершения процесса обновления выполните команду **ipconfig** для просмотра новой информации об IP-адресе. **имелось в виду ipconfig /renew ??**

```
C:\>ipconfig /renew

   IP Address......................: 192.168.1.6
   Subnet Mask.....................: 255.255.255.192
   Default Gateway.................: 192.168.1.1
   DNS Server......................: 0.0.0.0

C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.6
   Subnet Mask.....................: 255.255.255.192
   Default Gateway.................: ::
                                     192.168.1.1
```
        
3.  Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=3ms TTL=255

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 3ms, Average = 0ms
```        

## Настройка и проверка DHCP-ретрансляции на R2
    
В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1).

### Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1
    
1.  Настройте команду **ip helper-address** на G0/0/1, указав IP-адрес G0/0/0 R1.

```
R2(config)#int g0/0/1
R2(config-if)#ip helper-address 10.0.0.1
```        

** Дополнительная настройка на R1 **
```
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#net 192.168.1.96 255.255.255.240
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#default-router 192.168.1.97
```

1.  Сохраните конфигурацию.

```
R2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Попытка получить IP-адрес от DHCP на PC-B
    
1.  Из командной строки компьютера PC-B выполните команду **ipconfig /all**.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0003.E44B.05B1
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.102
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: ::
                                     192.168.1.97
   DHCP Servers....................: 192.168.1.97
   DHCPv6 IAID.....................: 27615200
   DHCPv6 Client DUID..............: 00-01-00-01-76-90-53-10-00-03-E4-4B-05-B1
   DNS Servers.....................: ::
                                     0.0.0.0
```
        
2.  После завершения процесса обновления выполните команду **ipconfig** для просмотра новой информации об IP-адресе.

```
C:\>ipconfig /renew

   IP Address......................: 192.168.1.102
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: 192.168.1.97
   DNS Server......................: 0.0.0.0

C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0003.E44B.05B1
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.102
   Subnet Mask.....................: 255.255.255.240
   Default Gateway.................: ::
                                     192.168.1.97
   DHCP Servers....................: 10.0.0.1
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-76-90-53-10-00-03-E4-4B-05-B1
   DNS Servers.....................: ::
                                     0.0.0.0

```
        
3.  Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

```

4.  Выполните **show ip dhcp binding** для R1 для проверки назначений адресов в DHCP.

```
R1#sh ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.1.6      0060.5C27.9250           --                     Automatic
```


        
5.  Выполните команду **show ip dhcp server statistics** для проверки сообщений DHCP.

** Не поддерживается в CPT **
```
R1#sh ip dhcp ?
  binding   DHCP address bindings
  conflict  DHCP address conflicts
  pool      DHCP pools information
  relay     Miscellaneous DHCP relay information
```