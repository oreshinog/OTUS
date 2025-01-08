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