# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями
![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab06/Img/Img00.png)
## Таблица адресации
  
|Устройство|Интерфейс|IP-адрес|Маска подсети|Шлюз по умолчанию|
|--|--|--|--|--|
|R1|G0/0/1.10|192.168.10.1|255.255.255.0|—|
|R1|G0/0/1.20|192.168.20.1|255.255.255.0|—|
|R1|G0/0/1.30|192.168.30.1|255.255.255.0|—|
|R1|G0/0/1.1000|—|—|—|
|S1|VLAN 10|192.168.10.11|255.255.255.0|192.168.10.1|
|S2|VLAN 10|192.168.10.12|255.255.255.0|192.168.10.1|
|PC-A|NIC|192.168.20.3|255.255.255.0|192.168.20.1|
|PC-B|NIC|192.168.30.3|255.255.255.0|192.168.30.1|

## Таблица VLAN
|VLAN|Имя|Назначенный интерфейс|
|-|-|-|
|10|Управление|S1: VLAN 10|
| | |S2: VLAN 10|
|20|Sales|S1: F0/6|
|30|Operations|S2: F0/18|
|999|Parking_Lot|С1: F0/2-4, F0/7-24, G0/1-2|
| | |С2: F0/2-17, F0/19-24, G0/1-2|
|1000|Собственная|—|

## Задачи
**Часть 1. Создание сети и настройка основных параметров устройства**
**Часть 2. Создание сетей VLAN и назначение портов коммутатора**
**Часть 3. Настройка транка 802.1Q между коммутаторами.**
**Часть 4. Настройка маршрутизации между сетями VLAN**
**Часть 5. Проверка, что маршрутизация между VLAN работает**

## Необходимые ресурсы
1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией

# Инструкции

## Часть 1.Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

### Шаг 1.1.Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab06/Img/Img01.png)

### Шаг 1.2.Настройте базовые параметры для маршрутизатора.

1.2.a.Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
```
Router>enable
Router#
```
1.2.b.Войдите в режим конфигурации.
```
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#
```
1.2.c.Назначьте маршрутизатору имя устройства.
```
Router(config)#hostname R1
R1(config)#
```
1.2.d.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
R1(config)#no ip domain lookup
```
1.2.e.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
```
R1(config)#enable secret class
```
1.2.f.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
```
R1(config)#line con 0
R1(config-line)#pass cisco
R1(config-line)#login
```
1.2.g.Установите **cisco** в качестве пароля виртуального терминала и активируйте вход.
```
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exi
R1(config)#line vty 5 15
R1(config-line)#pass cisco
R1(config-line)#login
```
1.2.h.Зашифруйте открытые пароли.
```
R1(config)#service password-encryption
```
1.2.i.Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
```
R1(config)#banner motd #
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #
```
1.2.j.Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```
1.2.k.Настройте на маршрутизаторе время.
```
R1(config)#clock timezone NSB 7
R1(config)#exit
R1#clock set 09:51:00 06 JAN 2025
```

Закройте окно настройки.

### Шаг 1.3.Настройте базовые параметры каждого коммутатора.

1.3.a.Присвойте коммутатору имя устройства.
1.3.b.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
1.3.c.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
1.3.d.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
1.3.e.Установите **cisco** в качестве пароля виртуального терминала и активируйте вход.
1.3.f.Зашифруйте открытые пароли.
1.3.g.Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
1.3.h.Настройте на коммутаторах время.
1.3.i.Сохранение текущей конфигурации в качестве начальной.

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#pass cisco
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
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #

S1(config)#clock timezone NSB 7
S1(config)#exit
S1#clock set 09:57:00 06 JAN 2025
S1#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#no ip domain lookup
S2(config)#enable secret class
S2(config)#line con 0
S2(config-line)#pass cisco
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
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #

S2(config)#clock timezone NSB 7
S2(config)#exit
S2#clock set 17:12:00 06 JAN 2025
S2#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```
Закройте окно настройки.

### Шаг 1.4.Настройте узлы ПК.

Адреса ПК можно посмотреть в таблице адресации.

![PC-A_IP](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab06/Img/Img02.png)

![PC-A_GW](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab06/Img/Img03.png)

![PC-B_IP](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab06/Img/Img04.png)

![PC-B_GW](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab06/Img/Img05.png)

## Часть 2.Создание сетей VLAN и назначение портов коммутатора

Во второй части вы создадите VLAN, как указано в таблице выше, на обоих коммутаторах. Затем вы назначите VLAN соответствующему интерфейсу и проверите настройки конфигурации. Выполните следующие задачи на каждом коммутаторе.

### Шаг 2.1.Создайте сети VLAN на коммутаторах.

2.1.a.Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.
```
S1(config)#vlan 10
S1(config-vlan)#name Management
S1(config-vlan)#exi
S1(config)#vlan 20
S1(config-vlan)#name Sales
S1(config-vlan)#exi
S1(config)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#exi
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#exi
S1(config)#vlan 1000
S1(config-vlan)#name Own
```

```
S2(config)#vlan 10
S2(config-vlan)#name Management
S2(config-vlan)#exi
S2(config)#vlan 20
S2(config-vlan)#name Sales
S2(config-vlan)#exi
S2(config)#vlan 30
S2(config-vlan)#name Operations
S2(config-vlan)#exi
S2(config)#vlan 999
S2(config-vlan)#name Parking_Lot
S2(config-vlan)#exi
S2(config)#vlan 1000
S2(config-vlan)#name Own
```

2.1.b.Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.

```
S1(config)#int vlan10
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

S1(config-if)#ip add 192.168.10.11 255.255.255.0
S1(config-if)#exi
S1(config)#ip default-gateway 192.168.10.1
```

```
S2(config)#int vlan10
S2(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

S2(config-if)#ip add 192.168.10.12 255.255.255.0
S2(config-if)#exi
S2(config)#ip default-gateway 192.168.10.1
```

2.1.c.Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.

```
S1(config)#int range fa 0/2-4
S1(config-if-range)#swi mode access
S1(config-if-range)#swi acc vlan 999
S1(config-if-range)#shu
S1(config-if-range)#exi
S1(config)#int range fa 0/7-24
S1(config-if-range)#swi mode access
S1(config-if-range)#swi acc vlan 999
S1(config-if-range)#shu
S1(config-if-range)#exi
S1(config)#int range gi 0/1-2
S1(config-if-range)#swi mode access
S1(config-if-range)#swi acc vlan 999
S1(config-if-range)#shu
S1(config-if-range)#exi
```

```
S2(config)#int range fa 0/2-17
S2(config-if-range)#swi mode access
S2(config-if-range)#swi acc vlan 999
S2(config-if-range)#shu
S2(config-if-range)#exi
S2(config)#int range fa 0/19-24
S2(config-if-range)#swi mode access
S2(config-if-range)#swi acc vlan 999
S2(config-if-range)#shu
S2(config-if-range)#exi
S2(config)#int range gi 0/1-2
S2(config-if-range)#swi mode access
S2(config-if-range)#swi acc vlan 999
S2(config-if-range)#shu
S2(config-if-range)#exi
```

**Примечание.** Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.

### Шаг 2.2.Назначьте сети VLAN соответствующим интерфейсам коммутатора.

2.2.a.Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

```
S1(config)#int fa 0/6
S1(config-if)#swi mode acc
S1(config-if)#swi acc vlan 20
S1(config-if)#exi
```

```
S2(config)#int fa 0/18
S2(config-if)#swi mode acc
S2(config-if)#swi acc vlan 30
S2(config-if)#exi
```

2.2.b.Убедитесь, что VLAN назначены на правильные интерфейсы.

```
S1#sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/5
10   Management                       active
20   Sales                            active    Fa0/6
30   Operations                       active
999  Parking_Lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 Own                              active
1002 fddi-default                     active
1003 token-ring-default               active
1004 fddinet-default                  active
1005 trnet-default                    active
S1#
```

```
S2#sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1
10   Management                       active
20   Sales                            active
30   Operations                       active    Fa0/18
999  Parking_Lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1000 Own active
1002 fddi-default active
1003 token-ring-default active
1004 fddinet-default active
1005 trnet-default active
S2#
```

Закройте окно настройки.

## Часть 3.Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

В части 3 вы вручную настроите интерфейс F0/1 как транк.

### Шаг 3.1.Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.

3.1.a.Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.
3.1.b.Установите native VLAN 1000 на обоих коммутаторах.
3.1.c.Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.

```
S1(config)#int fa 0/1
S1(config-if)#swi mode trunk
S1(config-if)#swi tru nat vlan 1000
S1(config-if)#swi tru all vlan 10,20,30,1000
S1(config-if)#exi

```

```
S2(config)#int fa 0/1
S2(config-if)#swi mode trunk
S2(config-if)#swi tru nat vlan 1000
S2(config-if)#swi tru all vlan 10,20,30,1000
S2(config-if)#exi
```

3.1.d.Проверьте транки, native VLAN и разрешенные VLAN через транк.

### Шаг 3.2.Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

3.2.a.Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

```
S1(config)#int fa 0/5
S1(config-if)#swi mode trunk
S1(config-if)#swi tru nat vlan 1000
S1(config-if)#swi tru all vlan 10,20,30,1000
S1(config-if)#exi
```

3.2.b.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

3.2.c.Проверка транкинга.

```
S1>sh int fa 0/5 swi
Name: Fa0/5
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: down
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
Trunking VLANs Enabled: 10,20,30,1000
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none
```

```
S1>sh int fa 0/1 swi
Name: Fa0/1
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
Trunking VLANs Enabled: 10,20,30,1000
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none
```

#### Вопрос:
Что произойдет, если G0/0/1 на R1 будет отключен?

#### Ответ: Роутер перестанет видеть сеть и соответственно нарушится связь устройств с роутером и доступ конечных устройств в другие VLAN

## Часть 1.Настройка маршрутизации между сетями VLAN

### Шаг 1.1.Настройте маршрутизатор.

Откройте окно конфигурации

1.1.a.При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.

```
R1(config)#int gi 0/0/1
R1(config-if)#no shu

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
```

1.1.b.Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
R1(config)#int gi 0/0/1.10
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.10, changed state to up

R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip add 192.168.10.1 255.255.255.0
R1(config-subif)#des Management
R1(config-subif)#exi
R1(config)#int gi 0/0/1.20
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.20, changed state to up

R1(config-subif)#encapsulation dot1q 20
R1(config-subif)#ip add 192.168.20.1 255.255.255.0
R1(config-subif)#des Sales
R1(config-subif)#exi
R1(config)#int gi 0/0/1.30
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.30, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.30, changed state to up

R1(config-subif)#encapsulation dot1q 30
R1(config-subif)#ip add 192.168.30.1 255.255.255.0
R1(config-subif)#des Operations
R1(config-subif)#exi
R1(config)#int gi 0/0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up

R1(config-subif)#des Own
R1(config-subif)#exi
```

```
R1#sh running-config | section interface GigabitEthernet0/0/1.1000
interface GigabitEthernet0/0/1.1000
description Own
no ip address
R1#
```

1.1.c.Убедитесь, что вспомогательные интерфейсы работают

```
S1>ping 192.168.10.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

S1>ping 192.168.20.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/1/4 ms

S1>ping 192.168.30.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.30.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

S1>
```

## Часть 2.Проверьте, работает ли маршрутизация между VLAN

### Шаг 2.1.Выполните следующие тесты с PC-A. Все должно быть успешно.

**Примечание.** Возможно, вам придется отключить брандмауэр ПК для работы ping

2.1.a.Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

```
C:\>ping 192.168.20.1

Pinging 192.168.20.1 with 32 bytes of data:

Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.20.1:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

2.1.b.Отправьте эхо-запрос с PC-A на PC-B.

```
C:\>ping 192.168.30.3

Pinging 192.168.30.3 with 32 bytes of data:

Request timed out.
Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
Reply from 192.168.30.3: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.30.3:
	Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

2.1.c.Отправьте команду ping с компьютера PC-A на коммутатор S2.

```
C:\>ping 192.168.10.12

Pinging 192.168.10.12 with 32 bytes of data:

Request timed out.
Request timed out.
Reply from 192.168.10.12: bytes=32 time=3ms TTL=254
Reply from 192.168.10.12: bytes=32 time<1ms TTL=254

Ping statistics for 192.168.10.12:
	Packets: Sent = 4, Received = 2, Lost = 2 (50% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 3ms, Average = 1ms
```

### Шаг 2.2.Пройдите следующий тест с PC-B

В окне командной строки на PC-B выполните команду **tracert** на адрес PC-A.

```
C:\>tracert 192.168.20.3

Tracing route to 192.168.20.3 over a maximum of 30 hops:

 1 0 ms 0 ms 0 ms 192.168.30.1
 2 0 ms 0 ms 0 ms 192.168.20.3

Trace complete.
```

#### Вопрос:

Какие промежуточные IP-адреса отображаются в результатах?

#### Ответ: адреса подинтерфейсов маршрутизатора, являющиеся шлюзами по-умолчанию для данных устройств