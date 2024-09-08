**Лабораторная работа №1. Базовая настройка коммутатора**

[Файл Cisco Packet Tracer](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Pkt/lab01.pkt)

# Топология
![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img1.png)
# Таблица адресации
|Устройство|Интерфейс|IP-адрес / префикс|
|--|--|--|
|S1|VLAN1|192.168.1.2/24|
|PC-A|NIC|192.168.1.10/24|
  
# Задачи
**Часть 1. Проверка конфигурации коммутатора по умолчанию**
**Часть 2. Создание сети и настройка основных параметров устройства**
Настройте базовые параметры коммутатора.
Настройте IP-адрес для ПК.
**Часть 3. Проверка сетевых подключений**
Отобразите конфигурацию устройства.
Протестируйте сквозное соединение, отправив эхо-запрос.
Протестируйте возможности удаленного управления с помощью Telnet.

## Часть 1.Создание сети и проверка настроек коммутатора по умолчанию
### Шаг 1.1.Создайте сеть согласно топологии.

1.1.a.Подсоедините консольный кабель, как показано в топологии. На данном этапе не подключайте кабель Ethernet компьютера PC-A.
![Console](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img1.png)

1.1.b.Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.

![Терминал](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img2.png)

#### Вопрос:
Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?
#### Ответ: 
Потому что на данном этапе коммутатор ещё не имеет IP адреса.

### Шаг 1.1.Проверьте настройки коммутатора по умолчанию.
1.1.a.Предположим, что коммутатор не имеет файла конфигурации, сохраненного в энергонезависимой памяти (NVRAM). Консольное подключение к коммутатору с помощью Tera Term или другой программы эмуляции терминала предоставит доступ к командной строке пользовательского режима EXEC в виде Switch>. Введите команду **enable**, чтобы войти в привилегированный режим EXEC.
![Enable](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img3.png)

Обратите внимание, что измененная в конфигурации строка будет отражать привилегированный режим EXEC.
Убедитесь, что на коммутаторе находится пустой файл конфигурации по умолчанию, с помощью команды **show running-config** привилегированного режима EXEC. Если конфигурационный файл был предварительно сохранен, его нужно удалить. В зависимости от модели коммутатора и версии IOS ваша конфигурация может слегка отличаться. Тем не менее, настроенных паролей или IP-адресов в конфигурации быть не должно. Выполните очистку настроек и перезагрузите коммутатор, если ваш коммутатор имеет настройки, отличные от настроек по умолчанию.
```
Switch#sh run
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
no ip address
shutdown
!
!
!
!
line con 0
!
line vty 0 4
login
line vty 5 15
login
!
!
!
!
end
```
1.1.b.Изучите  текущий  файл running configuration.
#### Вопросы:
Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?
#### Ответ - 24
Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960?
#### Ответ - 2
Каков диапазон значений, отображаемых в vty-линиях?
#### Ответ - от 0 до 15
c. Изучите файл загрузочной конфигурации (startup configuration), который содержится в
энергонезависимом ОЗУ (NVRAM).
Вопрос:
Почему появляется это сообщение?
```
Switch#sh startup-config
startup-config is not present
```
#### Ответ - Это ненастроенный коммутатор, в который ещё не сохранили конфигурацию
d. Изучите характеристики SVI для VLAN 1.
```
Switch#sh int vlan1
Vlan1 is administratively down, line protocol is down
Hardware is CPU Interface, address is 0000.0c75.cd8b (bia 0000.0c75.cd8b)
MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
ARP type: ARPA, ARP Timeout 04:00:00
Last input 21:40:21, output never, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
1682 packets input, 530955 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicast)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
563859 packets output, 0 bytes, 0 underruns
0 output errors, 23 interface resets
0 output buffer failures, 0 output buffers swapped out
```
#### Вопросы:
Назначен ли IP-адрес сети VLAN 1?
#### Ответ - нет
Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.
#### Ответ - 0000.0c75.cd8b
Данный интерфейс включен?
#### Ответ - интерфейс отключен

Изучите IP-свойства интерфейса SVI сети VLAN 1.
#### Вопрос:
Какие выходные данные вы видите?
#### Ответ:
```
sh ip interface vlan1
Vlan1 is administratively down, line protocol is down
Internet protocol processing disabled
```
f. Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК.

![LAN](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img5.png)

#### Вопрос:
Какие выходные данные вы видите?
#### Ответ:
```
sh ip interface vlan1
Vlan1 is administratively down, line protocol is down
Internet protocol processing disabled
```
g. Изучите сведения о версии ОС Cisco IOS на коммутаторе.
#### Вопросы:
Под управлением какой версии ОС Cisco IOS работает коммутатор?
```
Switch#sh version
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE
...
```
#### Ответ: 15.0(2)SE4, RELEASE
Как называется файл образа системы?
```
Switch#sh flash:
Directory of flash:/
1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin
64016384 bytes total (59345929 bytes free)
```
#### Ответ:  2960-lanbasek9-mz.150-2.SE4.bin
h. Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A.
```
Switch# sh int f0/6
FastEthernet0/6 is up, line protocol is up (connected)
Hardware is Lance, address is 0030.f2e0.bcbd (bia 0030.f2e0.bcbd)
BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s
...
```
#### Вопросы:
Интерфейс включен или выключен?
#### Ответ: интерфейс включен
Что нужно сделать, чтобы включить интерфейс?
#### Ответ: 
```
Switch#conf t
Switch(config)#int f0/1
Switch(config-if)#no sh
```
Какой MAC-адрес у интерфейса?
#### Ответ:  0030.f2e0.bcbd
Какие настройки скорости и дуплекса заданы в интерфейсе?
#### Ответ:  Full-duplex, 100Mb/s

i. Изучите флеш-память.
#### Вопрос: 
Какое имя присвоено образу Cisco IOS?
#### Ответ: 2960-lanbasek9-mz.150-2.SE4.bin

## Часть 2. Настройка базовых параметров сетевых устройств
Во второй части необходимо будет настроить основные параметры коммутатора и компьютера.
### Шаг 1. Настройте базовые параметры коммутатора.
a. В режиме глобальной конфигурации скопируйте следующие базовые параметры конфигурации и вставьте их в файл на коммутаторе S1.
```
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#service password-encryption
S1(config)#enable secret class
S1(config)#banner motd #
Unauthorized access is strictly prohibited. #
```
b.  Назначьте IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому вы получите возможность удаленного управления коммутатором.
```
S1(config)#int vlan1
S1(config-if)#ip add 192.168.1.2 255.255.255.0
S1(config-if)#no shu
```
c. Доступ через порт консоли также следует ограничить с помощью пароля. Используйте cisco в качестве пароля для входа в консоль в этом задании.
```
S1(config)#service password-encryption
S1(config-if)#line con 0
S1(config-line)#pass cisco
S1(config-line)#logging synchronous
S1(config-line)#login
```
d. Настройте каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.
```
S1(config)#line vty 0 4
S1(config-line)#pass cisco
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#pass cisco
S1(config-line)#exit
```
#### Вопрос: Для чего нужна команда login?
#### Ответ: Для требования запроса пароля

### Шаг 2. Настройте IP-адрес на компьютере PC-A.
Назначьте компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации.
1) Перейдите в Панель управления. (Control Panel)
2) В представлении «Категория» выберите « Просмотр состояния сети и задач».
3) Щелкните Изменение параметров адаптера на левой панели.
4) Щелкните правой кнопкой мыши интерфейс Ethernet и выберите «Свойства» .
5) Выберите Протокол Интернета версии 4 (TCP/IPv4) &gt; Свойства.
6) Выберите Использовать следующий IP-адрес и введите IP-адрес и маску подсети и
нажмите ОК.

![PCIP](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img6.png)

## Часть 3. Проверка сетевых подключений
В третьей части лабораторной работы вам предстоит проверить и задокументировать конфигурацию коммутатора, протестировать сквозное соединение между компьютером PC-A и коммутатором S1, а также протестировать возможность удаленного управления коммутатором.
### Шаг 1. Отобразите конфигурацию коммутатора.
```
S1#sh run
Building configuration...
Current configuration : 1328 bytes
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
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
ip address 192.168.1.2 255.255.255.0
!
banner motd ^C
Unauthorized access is strictly prohibited. ^C
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
```
b. Проверьте параметры VLAN 1.
```
S1#sh int vlan1
Vlan1 is up, line protocol is up
Hardware is CPU Interface, address is 0000.0c75.cd8b (bia 0000.0c75.cd8b)
Internet address is 192.168.1.2/24
MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
ARP type: ARPA, ARP Timeout 04:00:00
Last input 21:40:21, output never, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
1682 packets input, 530955 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicast)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
563859 packets output, 0 bytes, 0 underruns
0 output errors, 23 interface resets
0 output buffer failures, 0 output buffers swapped out
```
#### Вопрос: Какова полоса пропускания этого интерфейса?
#### Ответ: 100000 Kbit или 100 Mbit
### Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос.
a. В командной строке компьютера PC-A с помощью утилиты ping проверьте связь сначала с адресом PC-A.
```
Cisco Packet Tracer PC Command Line 1.0
C:\>ping 192.168.1.10

Pinging 192.168.1.10 with 32 bytes of data:

Reply from 192.168.1.10: bytes=32 time=4ms TTL=128
Reply from 192.168.1.10: bytes=32 time=2ms TTL=128
Reply from 192.168.1.10: bytes=32 time=3ms TTL=128
Reply from 192.168.1.10: bytes=32 time=4ms TTL=128

Ping statistics for 192.168.1.10:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 2ms, Maximum = 4ms, Average = 3ms
```
b. Из командной строки компьютера PC-A отправьте эхо-запрос на административный адрес интерфейса SVI коммутатора S1.
```
C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.2:
	Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
### Шаг 3. Проверьте удаленное управление коммутатором S1.

![Telnet](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab01/Img/img6.png)

#### Вопросы:
Зачем необходимо настраивать пароль VTY для коммутатора?
#### Ответ: Без пароля невозможно удалённое подключение через telnet
Что нужно сделать, чтобы пароли не отправлялись в незашифрованном виде?
#### Ответ: ```service password-encryption``` для паролей терминалов и использование ```enable secret``` вместо ```enable password```

## Приложение А. Инициализация и перезагрузка коммутатора
a. Подключитесь к коммутатору с помощью консоли и войдите в привилегированный режим EXEC.
b. Воспользуйтесь командой show flash, чтобы определить, были ли созданы сети VLAN на коммутаторе.
```
S1#sh flash
Directory of flash:/

1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin
3 -rw- 1318 <no date> config.text

64016384 bytes total (59344611 bytes free)
```
g. Если во флеш-памяти обнаружен файл vlan.dat, удалите его.
#### Файла нет
i. Введите команду erase startup-config, чтобы удалить файл загрузочной конфигурации из NVRAM.
```
S1#erase startup-config
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
```
j. Перезагрузите коммутатор, чтобы удалить устаревшую информацию о конфигурации из памяти.
```
S1#reload
Proceed with reload? [confirm]
System configuration has been modified. Save? [yes/no]: n
C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)
Cisco WS-C2960-24TT (RC32300) processor (revision C0) with 21039K bytes of memory.
2960-24TT starting...
Base ethernet MAC Address: 0000.0C75.CD8B
Xmodem file system is available.
Initializing Flash...
flashfs[0]: 1 files, 0 directories
flashfs[0]: 0 orphaned files, 0 orphaned directories
flashfs[0]: Total bytes: 64016384
flashfs[0]: Bytes used: 4670455
flashfs[0]: Bytes available: 59345929
flashfs[0]: flashfs fsck took 1 seconds.
...done Initializing Flash.

Boot Sector Filesystem (bs:) installed, fsid: 3
Parameter Block Filesystem (pb:) installed, fsid: 4
  

Loading "flash:/2960-lanbasek9-mz.150-2.SE4.bin"...
########################################################################## [OK]
Smart Init is enabled
smart init is sizing iomem
				TYPE MEMORY_REQ
				TOTAL: 0x00000000
Rounded IOMEM up to: 0Mb.
Using 6 percent iomem. [0Mb/512Mb]

			Restricted Rights Legend
Use, duplication, or disclosure by the Government is
subject to restrictions as set forth in subparagraph
(c) of the Commercial Computer Software - Restricted
Rights clause at FAR sec. 52.227-19 and subparagraph
(c) (1) (ii) of the Rights in Technical Data and Computer
Software clause at DFARS sec. 252.227-7013.
			cisco Systems, Inc.
			170 West Tasman Drive
			San Jose, California 95134-1706
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen
Initializing flashfs...
fsck: Disable shadow buffering due to heap fragmentation.
flashfs[2]: 2 files, 1 directories
flashfs[2]: 0 orphaned files, 0 orphaned directories
flashfs[2]: Total bytes: 32514048
flashfs[2]: Bytes used: 11952128
flashfs[2]: Bytes available: 20561920
flashfs[2]: flashfs fsck took 2 seconds.
flashfs[2]: Initialization complete....done Initializing flashfs.
Checking for Bootloader upgrade..
Boot Loader upgrade not required (Stage 2)
POST: CPU MIC register Tests : Begin
POST: CPU MIC register Tests : End, Status Passed
POST: PortASIC Memory Tests : Begin
POST: PortASIC Memory Tests : End, Status Passed
POST: CPU MIC interface Loopback Tests : Begin
POST: CPU MIC interface Loopback Tests : End, Status Passed
POST: PortASIC RingLoopback Tests : Begin
POST: PortASIC RingLoopback Tests : End, Status Passed
POST: PortASIC CAM Subsystem Tests : Begin
POST: PortASIC CAM Subsystem Tests : End, Status Passed
POST: PortASIC Port Loopback Tests : Begin
POST: PortASIC Port Loopback Tests : End, Status Passed
Waiting for Port download...Complete

This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.
A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html
If you require further assistance please contact us by sending email to
export@cisco.com.
cisco WS-C2960-24TT-L (PowerPC405) processor (revision B0) with 65536K bytes of memory.
Processor board ID FOC1010X104
Last reset from power-on
1 Virtual Ethernet interface
24 FastEthernet interfaces
2 Gigabit Ethernet interfaces
The password-recovery mechanism is enabled.
64K bytes of flash-simulated non-volatile configuration memory.
Base ethernet MAC Address : 00:00:0C:75:CD:8B
Motherboard assembly number : 73-10390-03
Power supply part number : 341-0097-02
Motherboard serial number : FOC10093R12
Power supply serial number : AZS1007032H
Model revision number : B0
Motherboard revision number : B0
Model number : WS-C2960-24TT-L
System serial number : FOC1010X104
Top Assembly Part Number : 800-27221-02
Top Assembly Revision Number : A0
Version ID : V02
CLEI Code Number : COM3L00BRA
Hardware Board Revision Number : 0x01

Switch Ports Model SW Version SW Image
------ ----- ----- ---------- ----------
* 1 26 WS-C2960-24TT-L 15.0(2)SE4 C2960-LANBASEK9-M

Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

  

Press RETURN to get started!

  
%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/6, changed state to up


 Switch>
```