# Лабораторная работа - Настройка IPv6-адресов на сетевых устройствах
![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img01.png)
# Таблица адресации
  

|Устройство|Интерфейс|IPv6-адрес|Link local IPv6-адрес|Длина префикса|Шлюз по умолчанию|
|--|--|--|--|--|--|
|R1|G0/0/0|2001:db8:acad:a::1|fe80::1|64|-|
||G0/0/1|2001:db8:acad:1::1|fe80::1|64|-|
|S1|VLAN 1|2001:db8:acad:1::b|fe80::b|64|-|
|PC-A|NIC|2001:db8:acad:1::3|SLACC|64|fe80::1|
|PC-B|NIC|2001:db8:acad:a::3|SLACC|64|fe80::1|


## Задачи

**Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора**

**Часть 2. Ручная настройка IPv6-адресов**

**Часть 3. Проверка сквозного соединения**

## Общие сведения/сценарий
В этой лабораторной работе вы будете настраивать хосты и интерфейсы устройств с IPv6-адресами. Для просмотра индивидуальных и групповых IPv6-адресов вы будете использовать команду **show**. Вы также будете проверять сквозное соединение с помощью команд **ping** and **traceroute**.
## Необходимые ресурсы
1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией
## Инструкции
Заполните приведенные ниже таблицы, зная заданный IPv4-адрес, исходную и новую маску подсети.

###  Часть 1.Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

***Шаг 1.1.Настройте маршрутизатор.***
```
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname R1
R1(config)#service password-encryption
R1(config)#enable secret class
R1(config)#banner motd #
Unauthorized access is strictly prohibited. #
R1(config)#line con 0
R1(config-line)#pass cisco
R1(config-line)#logging synchronous
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#exit
R1(config)#line vty 5 15
R1(config-line)#pass cisco
R1(config-line)#exit
```

***Шаг 1.2.Настройте коммутатор.***
```
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#service password-encryption
S1(config)#enable secret class
S1(config)#banner motd #
Unauthorized access is strictly prohibited. #
S1(config)#line con 0
S1(config-line)#pass cisco
S1(config-line)#logging synchronous
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#pass cisco
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#pass cisco
S1(config-line)#exit
```
### Часть 2.Ручная настройка IPv6-адресов

***Шаг 2.1.Назначьте IPv6-адреса интерфейсам Ethernet на R1.***

2.1.a.Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.

```
R1(config)#int gi0/0/0
R1(config-if)#ipv6 add
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#exit
R1(config)#int gi0/0/1
R1(config-if)#ipv6 add 2001:db8:acad:1::1/64
R1(config-if)#exit
```

2.1.b.Введите команду show ipv6 interface brief, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.

```
R1#show ipv6 interface brief
GigabitEthernet0/0/0 [administratively down/down]
	FE80::206:2AFF:FEEE:C701
	2001:DB8:ACAD:A::1
GigabitEthernet0/0/1 [administratively down/down]
	FE80::206:2AFF:FEEE:C702
	2001:DB8:ACAD:1::1
Vlan1 [administratively down/down]
	unassigned
```
2.1.c.Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.

```
R1(config)#int gi0/0/0
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)#no shu
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
R1(config-if)#exit
R1(config)#int gi0/0/1
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)#no shu
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
```

2.1.d.Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.

```
R1#show ipv6 interface brief
GigabitEthernet0/0/0 [up/up]
	FE80::1
	2001:DB8:ACAD:A::1
GigabitEthernet0/0/1 [up/up]
	FE80::1
	2001:DB8:ACAD:1::1
Vlan1 [administratively down/down]
	unassigned
```

### Шаг 1.1.Активируйте IPv6-маршрутизацию на R1.

***1.1.a.В командной строке на PC-B введите команду **ipconfig**, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.***

```
C:\>ipconfig
FastEthernet0 Connection:(default port)
Connection-specific DNS Suffix..:
Link-local IPv6 Address.........: FE80::201:64FF:FE3B:8165
IPv6 Address....................: ::
IPv4 Address....................: 0.0.0.0
Subnet Mask.....................: 0.0.0.0
Default Gateway.................: ::
0.0.0.0
Bluetooth Connection:
Connection-specific DNS Suffix..:
Link-local IPv6 Address.........: ::
IPv6 Address....................: ::
IPv4 Address....................: 0.0.0.0
Subnet Mask.....................: 0.0.0.0
Default Gateway.................: ::
0.0.0.0
```
***Назначен ли индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B?***
На сетевой карте есть link-local адрес FE80::201:64FF:FE3B:8165, но не назначен маршрутизируемый ipv6 адрес.

***1.1.a.Активируйте IPv6-маршрутизацию на R1 с помощью команды IPv6 unicast-routing.***
```
R1(config)#ipv6 unicast-routing
```
***1.1.b.Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду **ipconfig** на PC-B. Проверьте данные IPv6-адреса.***

```
C:\>ipconfig
FastEthernet0 Connection:(default port)
	Connection-specific DNS Suffix..:
	Link-local IPv6 Address.........: FE80::201:64FF:FE3B:8165
	IPv6 Address....................: 2001:DB8:ACAD:A:201:64FF:FE3B:8165
	IPv4 Address....................: 0.0.0.0
	Subnet Mask.....................: 0.0.0.0
	Default Gateway.................: FE80::1
	0.0.0.0
Bluetooth Connection:
	Connection-specific DNS Suffix..:
	Link-local IPv6 Address.........: ::
	IPv6 Address....................: ::
	IPv4 Address....................: 0.0.0.0
	Subnet Mask.....................: 0.0.0.0
	Default Gateway.................: ::
	0.0.0.0
```

***Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?***
Интерфейсы роутера R1 вступили в группу маршрутизаторов FF02::2 и отправили RA пакет с информацией о префиксе,  со своего локального адреса FE80::1, который будет использоваться как шлюз по-умолчанию.

### Шаг 1.1.Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.

***1.1.a.Назначьте адрес IPv6 для S1. Также назначьте этому интерфейсу локальный адрес канала fe80::b.***

```
S1(config)#sdm prefer dual-ipv4-and-ipv6 default
Changes to the running SDM preferences have been stored, but cannot take effect until the next reload.
Use 'show sdm prefer' to see what SDM preference is currently active.
S1(config)#end
S1#reload

S1(config)#int vlan1
S1(config-if)#ipv6 add 2001:db8:acad:1::b/64
S1(config-if)#ipv6 add fe80::b link-local
S1(config-if)#no shu
```
***1.1.b.Проверьте правильность назначения IPv6-адресов интерфейсу управления с помощью команды show ipv6 interface vlan1.***

```
S1#sh ipv6 int Vlan 1
Vlan1 is up, line protocol is up
	IPv6 is enabled, link-local address is FE80::B
	No Virtual link-local address(es):
	Global unicast address(es):
		2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64
	Joined group address(es):
		FF02::1
		FF02::1:FF00:B
	MTU is 1500 bytes
	ICMP error messages limited to one every 100 milliseconds
	ICMP redirects are enabled
	ICMP unreachables are sent
	Output features: Check hwidb
	ND DAD is enabled, number of DAD attempts: 1
	ND reachable time is 30000 milliseconds
```

### Шаг 1.2.Назначьте компьютерам статические IPv6-адреса.

***1.2.a.Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6.***
***1.2.b.Убедитесь, что оба компьютера имеют правильную информацию адреса IPv6. Каждый компьютер должен иметь два глобальных адреса IPv6: один статический и один SLACC.***
**Примечание**. При выполнении работы в среде Cisco Packet Tracer установите статический и SLACC адреса на компьютеры последовательно, отразив результаты в отчете
PC-A static

![PC-A static1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img02.png)

![PC-A static2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img03.png)

PC-A SLAAC

![PC-A SLAAC1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img04.png)

![PC-A SLAAC2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img05.png)

PC-B static

![PC-B static1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img06.png)

![PC-B static2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img07.png)

PC-B SLAAC

![PC-B SLAAC1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img08.png)

![PC-B SLAAC2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab04/Img/img09.png)

### Часть 2.Проверка сквозного подключения
С PC-A отправьте эхо-запрос на **FE80::1**. Это локальный адрес канала, назначенный G0/1 на R1.
```
C:\>ping FE80::1
Pinging FE80::1 with 32 bytes of data:

Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255

Ping statistics for FE80::1:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

Отправьте эхо-запрос на интерфейс управления S1 с PC-A.

```
C:\>ping 2001:db8:acad:1::b

Pinging 2001:db8:acad:1::b with 32 bytes of data:
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255

Ping statistics for 2001:DB8:ACAD:1::B:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

Введите команду **tracert** на PC-A, чтобы проверить наличие сквозного подключения к PC-B.

```
C:\>tracert 2001:db8:acad:a::3

Tracing route to 2001:db8:acad:a::3 over a maximum of 30 hops:

1 0 ms 0 ms 0 ms 2001:DB8:ACAD:1::1
2 0 ms 0 ms 0 ms 2001:DB8:ACAD:A::3

Trace complete.
```
С PC-B отправьте эхо-запрос на PC-A.

```
C:\>ping 2001:db8:acad:1::3

Pinging 2001:db8:acad:1::3 with 32 bytes of data:

Reply from 2001:DB8:ACAD:1::3: bytes=32 time=1ms TTL=127
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127

Ping statistics for 2001:DB8:ACAD:1::3:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 1ms, Average = 0ms
```

С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1.

```
C:\>ping fe80::1

Pinging fe80::1 with 32 bytes of data:

Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255

Ping statistics for FE80::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### Вопросы для повторения

***Часть 1.Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?***

Локальный адрес не маршрутизируется и используется только в пределах локальной сети интерфейса - соответственно проблем не будет.

***Часть 2.Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?***

Первые 64 бита, включая пропущенные нули - 2001:db8:acad:0000