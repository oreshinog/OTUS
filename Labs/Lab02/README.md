# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора 
[**Файл Cisco Packet Tracer**](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab02/Pkt/lab02.pkt)
# Топология
![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab02/Img/lab02.png)
# Таблица адресации
|Устройство|Интерфейс|IP-адрес / префикс|Маска подсети|
|--|--|--|--|
|S1|VLAN1|192.168.1.11|255.255.255.0
|S2|VLAN1|192.168.1.12|255.255.255.0
|PC-A|NIC|192.168.1.1|255.255.255.0
|PC-B|NIC|192.168.1.2|255.255.255.0
  # Цели
**Часть 1. Создание и настройка сети** 
**Часть 2. Изучение таблицы МАС-адресов коммутатора**

# Задачи
# Инструкции

## Часть 1.Создание и настройка сети

### Шаг 1.1.Подключите сеть в соответствии с топологией.
![Сеть](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab02/Img/img01.png)
### Шаг 1.2.Настройте узлы ПК.
![ПК1Сеть](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab02/Img/img02.png)
![ПК2Сеть](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab02/Img/img03.png)
### Шаг 1.3.Выполните инициализацию и перезагрузку коммутаторов, настройте базовые параметры каждого коммутатора.
1.3.a.Настройте имена устройств в соответствии с топологией.
1.3.b.Настройте IP-адреса, как указано в таблице адресации.
1.3.c.Назначьте **cisco** в качестве паролей консоли и VTY.
1.3.d.Назначьте **class** в качестве пароля доступа к привилегированному режиму EXEC.
### S1
```
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#service password-encryption
S1(config)#enable secret class
S1(config)#banner motd #
Unauthorized access is strictly prohibited. #

S1(config)#int vlan1
S1(config-if)#ip add 192.168.1.11 255.255.255.0
S1(config-if)#no shu
S1(config-if)#exit

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

S1(config)#exit
S1#copy run start
S1#reload
```
### S2
```
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S2
S2(config)#service password-encryption
S2(config)#enable secret class
S2(config)#banner motd #
Unauthorized access is strictly prohibited. #

S2(config)#int vlan1
S2(config-if)#ip add 192.168.1.12 255.255.255.0
S2(config-if)#no shu
S2(config-if)#exit

S2(config)#line con 0
S2(config-line)#pass cisco
S2(config-line)#logging synchronous
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#pass cisco
S2(config-line)#exit
S2(config)#line vty 5 15
S2(config-line)#pass cisco
S2(config-line)#exit

S2(config)#exit
S2#copy run start
S2#reload
```
## Часть 2.Изучение таблицы МАС-адресов коммутатора
### Шаг 2.1.Запишите МАС-адреса сетевых устройств.
#### 2.1.a.Откройте командную строку на PC-A и PC-B и введите команду **ipconfig /all**.
##### PC-A
```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

	Connection-specific DNS Suffix..:
	Physical Address................: 00E0.8F6A.61AE
	Link-local IPv6 Address.........: FE80::2E0:8FFF:FE6A:61AE
	IPv6 Address....................: ::
	IPv4 Address....................: 192.168.1.1
	Subnet Mask.....................: 255.255.255.0
	Default Gateway.................: ::
									  0.0.0.0
	DHCP Servers....................: 0.0.0.0
	DHCPv6 IAID.....................:
	DHCPv6 Client DUID..............: 00-01-00-01-BA-5D-A5-7D-00-E0-8F-6A-61-AE
	DNS Servers.....................: ::
								   	  0.0.0.0
  
Bluetooth Connection:

	Connection-specific DNS Suffix..:
	Physical Address................: 0001.96CE.6330
	Link-local IPv6 Address.........: ::
	IPv6 Address....................: ::
	IPv4 Address....................: 0.0.0.0
	Subnet Mask.....................: 0.0.0.0
	Default Gateway.................: ::
									  0.0.0.0
	DHCP Servers....................: 0.0.0.0
	DHCPv6 IAID.....................:
	DHCPv6 Client DUID..............: 00-01-00-01-BA-5D-A5-7D-00-E0-8F-6A-61-AE
	DNS Servers.....................: ::
									  0.0.0.0
```
##### PC-B
```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

	Connection-specific DNS Suffix..:
	Physical Address................: 0001.63BE.6E9A
	Link-local IPv6 Address.........: FE80::201:63FF:FEBE:6E9A
	IPv6 Address....................: ::
	IPv4 Address....................: 192.168.1.2
	Subnet Mask.....................: 255.255.255.0
	Default Gateway.................: ::
									  0.0.0.0
	DHCP Servers....................: 0.0.0.0
	DHCPv6 IAID.....................:
	DHCPv6 Client DUID..............: 00-01-00-01-13-1C-D1-86-00-01-63-BE-6E-9A
	DNS Servers.....................: ::
									  0.0.0.0

Bluetooth Connection:

	Connection-specific DNS Suffix..:
	Physical Address................: 0060.47AC.D15D
	Link-local IPv6 Address.........: ::
	IPv6 Address....................: ::
	IPv4 Address....................: 0.0.0.0
	Subnet Mask.....................: 0.0.0.0
	Default Gateway.................: ::
									  0.0.0.0
	DHCP Servers....................: 0.0.0.0
	DHCPv6 IAID.....................:
	DHCPv6 Client DUID..............: 00-01-00-01-13-1C-D1-86-00-01-63-BE-6E-9A
	DNS Servers.....................: ::
									  0.0.0.0
```
#### Назовите физические адреса адаптера Ethernet.
MAC-адрес компьютера PC-A: **00E0.8F6A.61AE**
MAC-адрес компьютера PC-B: **0001.63BE.6E9A**

#### 1.1.a.Подключитесь к коммутаторам S1 и S2 через консоль и введите команду **show interface F0/1** на каждом коммутаторе.
##### S1
```
S1#sh int f0/1
FastEthernet0/1 is up, line protocol is up (connected)
Hardware is Lance, address is 0002.4aae.4a01 (bia 0002.4aae.4a01)
BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
0 output errors, 0 collisions, 10 interface resets
0 babbles, 0 late collision, 0 deferred
0 lost carrier, 0 no carrier
0 output buffer failures, 0 output buffers swapped out
```
##### S2
```
S2#sh int f0/1
FastEthernet0/1 is up, line protocol is up (connected)
Hardware is Lance, address is 00d0.bc37.ad01 (bia 00d0.bc37.ad01)
BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
0 output errors, 0 collisions, 10 interface resets
0 babbles, 0 late collision, 0 deferred
0 lost carrier, 0 no carrier
0 output buffer failures, 0 output buffers swapped out
```

#### Вопросы:
Назовите адреса оборудования во второй строке выходных данных команды (или зашитый адрес — bia).
МАС-адрес коммутатора S1 Fast Ethernet 0/1: **0002.4aae.4a01**
МАС-адрес коммутатора S2 Fast Ethernet 0/1: **00d0.bc37.ad01**

### Шаг 1.1.Просмотрите таблицу МАС-адресов коммутатора.
Подключитесь к коммутатору S2 через консоль и просмотрите таблицу МАС-адресов до и после тестирования сетевой связи с помощью эхо-запросов.

1.1.a.Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC.
1.1.b.В привилегированном режиме EXEC введите команду **show mac address-table** и нажмите клавишу ввода.
```
S2#sh mac add
Mac Address Table
-------------------------------------------

Vlan Mac Address Type Ports
---- ----------- -------- -----
 
1 0002.4aae.4a01 DYNAMIC Fa0/1
```
Даже если сетевая коммуникация в сети не происходила (т. е. если команда ping не отправлялась), коммутатор может узнать МАС-адреса при подключении к ПК и другим коммутаторам.

#### Вопросы:
Записаны ли в таблице МАС-адресов какие-либо МАС-адреса?
#### Ответ: В таблице записан MAC адрес
Какие МАС-адреса записаны в таблице? С какими портами коммутатора они сопоставлены и каким устройствам принадлежат? Игнорируйте МАС-адреса, сопоставленные с центральным процессором.
#### Ответ: В таблице записан MAC адрес подключенного к коммутатору ПК PC-B, в порт Fa0/1
Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды **show mac address-table**? Работает ли это решение в любой ситуации?
#### Ответ: Первые три шестнадцатиричных  значения в MAC адресе - код OUI поставщика, по ним можно приблизительно определить устройство, работает не всегда, например код OUI у маршрутизатора и коммутатора одного производителя будет совпадать.
### Шаг 1.1.Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.
1.1.a.В привилегированном режиме EXEC введите команду **clear mac address-table dynamic** и нажмите клавишу **Enter**.
1.1.b.Снова быстро введите команду **show mac address-table**.
```
S2#clear mac address-table dynamic
S2#sh mac add
Mac Address Table
-------------------------------------------

Vlan Mac Address Type Ports
---- ----------- -------- -----
 
1 0002.4aae.4a01 DYNAMIC Fa0/1
```
#### Вопросы:
Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса?
#### Ответ: В таблице не указаны адреса для VLAN 1. Указан MAC адрес подключенного к коммутатору ПК
Через 10 секунд введите команду **show mac address-table** и нажмите клавишу ввода. 
```
S2#sh mac add
Mac Address Table
-------------------------------------------

Vlan Mac Address Type Ports
---- ----------- -------- -----
 
1 0002.4aae.4a01 DYNAMIC Fa0/1
```
Появились ли в таблице МАС-адресов новые адреса?
#### Ответ: Нет
### Шаг 1.1.С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.
1.1.a.Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time=2ms TTL=128

Ping statistics for 192.168.1.1:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 2ms, Average = 0ms

C:\>ping 192.168.1.11

Pinging 192.168.1.11 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.11:
	Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms

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
```
1.1.a.На компьютере PC-B откройте командную строку и еще раз введите команду **arp -a**.
```
C:\>arp -a
Internet Address Physical Address Type
192.168.1.1 00e0.8f6a.61ae dynamic
192.168.1.11 0001.43ca.49d5 dynamic
192.168.1.12 0090.2b94.a680 dynamic
```
#### Вопрос:
Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?
#### Ответ: Получено три пары

#### Вопрос:
От всех ли устройств получены ответы? Если нет, проверьте кабели и IP-конфигурации.
#### Ответ: Получены ответы от всех устройств

1.1.a.Подключившись через консоль к коммутатору S2, введите команду **show mac address-table**.
```
S2#sh mac add
Mac Address Table
-------------------------------------------

Vlan Mac Address Type Ports
---- ----------- -------- -----

1 0001.43ca.49d5 DYNAMIC Fa0/1
1 0001.63be.6e9a DYNAMIC Fa0/18
1 0002.4aae.4a01 DYNAMIC Fa0/1
1 00e0.8f6a.61ae DYNAMIC Fa0/1
```
#### Вопрос:
Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства?
#### Ответ: Коммутатор добавил в таблицу МАС-адресов, адреса:
#### 0001.43ca.49d5 - S1 VLAN1
#### 0001.63be.6e9a - PC-B
#### 0002.4aae.4a01 - S1 Fa0/1
#### 00e0.8f6a.61ae - PC-A
На компьютере PC-B откройте командную строку и еще раз введите команду **arp -a**.
```
C:\>arp -a
Internet Address Physical Address Type
192.168.1.1 00e0.8f6a.61ae dynamic
192.168.1.11 0001.43ca.49d5 dynamic
192.168.1.12 0090.2b94.a680 dynamic
```
#### Вопрос:
Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы?
#### Ответ: Да, в ARP кэше появились записи для всех сетевых устройств, которым были отправлены эхо-запросы

## Вопрос для повторения
В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?
#### Ответ: При переполнении таблицы MAC адресов коммутатор начнёт отправлять траффик на все порты, кроме того порта, с которого его получил, тем самым повышая нагрузку на сеть и замедляя её