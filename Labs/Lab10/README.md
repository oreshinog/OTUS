# Лабораторная работа - Настройка протокола OSPFv2 для одной области

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab10/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IP-адрес|Маска подсети|
|--|--|--|--|
|R1|G0/0/1|10.53.0.1|255.255.255.0|
||Loopback 0|172.16.1.1|255.255.255.0|
|R2|G0/0/1|10.53.0.2|255.255.255.0|
||Loopback 0|192.168.1.1|255.255.255.0|

# Задачи
**Часть 1. Создание сети и настройка основных параметров устройства**

**Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области**

**Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области**

# Необходимые ресурсы

2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией

# Инструкции

## Часть 1.Создание сети и настройка основных параметров устройства

### Шаг 1.1.Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab10/Img/Img01.png)

### Шаг 1.2.Произведите базовую настройку маршрутизаторов.

1.2.a.Назначьте маршрутизатору имя устройства.
1.2.b.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
1.2.c.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
1.2.d.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
1.2.e.Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
1.2.f.Зашифруйте открытые пароли.
1.2.g.Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
1.2.h.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

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

R1(config)#exit
R1#copy run sta
%SYS-5-CONFIG_I: Configured from console by console

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

R2(config)#exit
R2#copy run sta
%SYS-5-CONFIG_I: Configured from console by console

R2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Шаг 1.3.Настройте базовые параметры каждого коммутатора.

1.3.a.Назначьте коммутатору имя устройства.
1.3.b.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
1.3.c.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
1.3.d.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
1.3.e.Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
1.3.f.Зашифруйте открытые пароли.
1.3.g.Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
1.3.h.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

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

S1(config)#exit
S1#copy run sta
%SYS-5-CONFIG_I: Configured from console by console

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

S2(config)#exit
S2#copy run sta
%SYS-5-CONFIG_I: Configured from console by console

S2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

## Часть 2.Настройка и проверка базовой работы протокола OSPFv2 для одной области

### Шаг 2.1.Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.

2.1.a.Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.

```
R1(config)#int g0/0/1
R1(config-if)#ip add 10.53.0.1 255.255.255.0
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R1(config-if)#ex
R1(config)#int Loopback1 

R1(config-if)#
%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R1(config-if)#ip add 172.16.1.1 255.255.255.0
```

```
R2(config)#int gi0/0/1
R2(config-if)#ip add 10.53.0.2 255.255.255.0
R2(config-if)#no sh

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R2(config-if)#ex
R2(config)#int Loopback1 

R2(config-if)#
%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R2(config-if)#ip add 192.168.1.1 255.255.255.0
```

2.1.b.Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.
2.1.c.Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).
2.1.d.Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

```
R1(config)#router ospf 56
R1(config-router)#router-id 1.1.1.1
R1(config-router)#network 10.53.0.0 0.0.0.255 area 0
```

```
R2(config)#router ospf 56
R2(config-router)#router-id 2.2.2.2
R2(config-router)#network 10.53.0.0 0.0.0.255 area 0
```


2.1.e.Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

```
R2(config)#int loopback 1
R2(config-if)#ip ospf 56 area 0
```

2.1.f.Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.

```
R1#sh ip ospf database 
            OSPF Router with ID (1.1.1.1) (Process ID 56)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         83          0x80000002 0x009239 1
2.2.2.2         2.2.2.2         83          0x80000003 0x0083c1 2

                Net Link States (Area 0)
Link ID         ADV Router      Age         Seq#       Checksum
10.53.0.1       1.1.1.1         83          0x80000001 0x001e79
```

#### Вопрос:

Какой  маршрутизатор  является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?

```
R2#show ip ospf neighbor


Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/DR         00:00:36    10.53.0.1       GigabitEthernet0/0/1
```

```
R1#show ip ospf neighbor


Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:37    10.53.0.2       GigabitEthernet0/0/1
```

**R1 - DR, R2 - BDR.  **

1.1.a.На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.

```
R1#show ip route ospf
     192.168.1.0/32 is subnetted, 1 subnets
O       192.168.1.1 [110/2] via 10.53.0.2, 00:02:05, GigabitEthernet0/0/1
```

1.1.b.Запустите Ping до адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.

```
R1#ping 192.168.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/4 ms
```

Закройте окно настройки.

## Часть 2.Оптимизация и проверка конфигурации OSPFv2 для одной области

### Шаг 2.1.Реализация различных оптимизаций на каждом маршрутизаторе.

2.1.a.На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

```
R1(config)#int g0/0/1
R1(config-if)#ip ospf priority 50
```

```
R2#sh ip ospf nei


Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1          50   FULL/DR         00:00:34    10.53.0.1       GigabitEthernet0/0/1
```

2.1.b.Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

```
R1(config)#int g0/0/1
R1(config-if)#ip ospf hello-interval 30
```

```
R2(config)#int g0/0/1
R2(config-if)#ip ospf hello-interval 30
```

2.1.c.На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

```
R1(config)#ip route 0.0.0.0 0.0.0.0 loopback1
%Default route without gateway, if not a point-to-point interface, may impact performance
R1(config)#router ospf 56
R1(config-router)#default-information originate 
```

2.1.d.добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.

```
R2(config)#int loopback1
R2(config-if)#ip ospf network point-to-point
```

2.1.e.Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.

```
R2(config)#router ospf 56
R2(config-router)#passive-interface loopback1
```

2.1.f.Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды **clear ip ospf process** . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

```
R1(config)#router ospf 56
R1(config-router)#auto-cost reference-bandwidth 100
R1(config-router)#end
R1#clear ip ospf process
```

```
R2(config)#router ospf 56
R2(config-router)#auto-cost reference-bandwidth 100
R2(config-router)#end
R2#clear ip ospf process
```

### Шаг 2.2.Убедитесь, что оптимизация OSPFv2 реализовалась.

2.2.a.Выполните команду **show ip ospf interface g0/0/1** на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast

** Ранее не требовалось менять данный параметр **
```
R1(config)#int g0/0/1
R1(config-if)#ip ospf dead-interval 120
R1(config)#end
R1#clear ip osp process
```

```
R2(config)#int g0/0/1
R2(config-if)#ip ospf dead-interval 120
R2(config)#end
R2#clear ip osp process
```

```
R1#show ip ospf interface g0/0/1

GigabitEthernet0/0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 1
  Transmit Delay is 1 sec, State DR, Priority 50
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  No backup designated router on this network
  Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
    Hello due in 00:00:25
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 2.2.2.2
  Suppress hello for 0 neighbor(s)
```

2.2.b.На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.

```
R1#sh ip route ospf
O    192.168.1.0 [110/2] via 10.53.0.2, 00:30:19, GigabitEthernet0/0/1
```

```
R1#sh ip route ospf
O    192.168.1.0 [110/2] via 10.53.0.2, 00:30:19, GigabitEthernet0/0/1

R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.53.0.0/24 is directly connected, GigabitEthernet0/0/1
L       10.53.0.1/32 is directly connected, GigabitEthernet0/0/1
     172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C       172.16.1.0/24 is directly connected, Loopback1
L       172.16.1.1/32 is directly connected, Loopback1
O    192.168.1.0/24 [110/2] via 10.53.0.2, 00:31:04, GigabitEthernet0/0/1
S*   0.0.0.0/0 is directly connected, Loopback1
```

2.2.c.Введите команду **show ip route ospf** на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.

```
R2#sh ip route ospf
O*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:03:30, GigabitEthernet0/0/1
```

2.2.d.Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.

```
R2#ping 172.16.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```


#### Вопрос:

Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?

```
R1#sh ip route 192.168.1.1
Routing entry for 192.168.1.0/24
Known via "ospf 56", distance 110, metric 2, type intra area
  Last update from 10.53.0.2 on GigabitEthernet0/0/1, 01:26:23 ago
  Routing Descriptor Blocks:
  * 10.53.0.2, from 2.2.2.2, 01:26:23 ago, via GigabitEthernet0/0/1
      Route metric is 2, traffic share count is 1
```

```
R2#sh ip route 0.0.0.0
Routing entry for 0.0.0.0/0, supernet
Known via "ospf 56", distance 110, metric 1, candidate default path
  Tag 1, type extern 2, forward metric 1
  Last update from 10.53.0.1 on GigabitEthernet0/0/1, 00:56:23 ago
  Routing Descriptor Blocks:
  * 10.53.0.1, from 1.1.1.1, 00:56:23 ago, via GigabitEthernet0/0/1
      Route metric is 1, traffic share count is 1
```
**При Reference Bandwidth равной 100мбит, стоимость всех интерфейсов равна 1, суммарная стоимость маршрута (R2:g0/0/1)+(R2:Loopback1) больше стоимости одного перехода на (R1:Loopback1)**