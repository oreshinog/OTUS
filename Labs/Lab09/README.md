# Лабораторная работа - Конфигурация безопасности коммутатора

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab09/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IP-адрес|Маска подсети|
|--|--|--|--|
|R1|G0/0/1|192.168.10.1|255.255.255.0|
||Loopback 0|10.10.1.1|255.255.255.0|
|S1|VLAN 10|192.168.10.201|255.255.255.0|
|S2|VLAN 10|192.168.10.202|255.255.255.0|
|PC-A|NIC|DHCP|255.255.255.0|
|PC-B|NIC|DHCP|255.255.255.0|

# Задачи

**Часть 1. Настройка основного сетевого устройства**
Создайте сеть.
Настройте маршрутизатор R1.
Настройка и проверка основных параметров коммутатора
**Часть 2. Настройка сетей VLAN**
Сконфигруриуйте VLAN 10.
Сконфигруриуйте SVI для VLAN 10.
Настройте VLAN 333 с именем Native на S1 и S2.
Настройте VLAN 999 с именем ParkingLot на S1 и S2.
**Часть 3: Настройки безопасности коммутатора.**
Реализация магистральных соединений 802.1Q.
Настройка портов доступа
Безопасность неиспользуемых портов коммутатора
Документирование и реализация функций безопасности порта.
Реализовать безопасность DHCP snooping .
Реализация PortFast и BPDU Guard
Проверка сквозной связанности.

# Необходимые ресурсы

1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.3 или аналогичным)
2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель)
2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией

# Инструкции

## Часть 1.Настройка основного сетевого устройства

### Шаг 1.1.Создайте сеть.

### 1.  Создайте сеть согласно топологии.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab09/Img/Img01.png)
    
### Шаг 1.2.Настройте маршрутизатор R1.

### 1.  Загрузите следующий конфигурационный скрипт на R1.
    

```
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
!
ip dhcp pool Students
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
domain-name CCNA2.Lab-11.6.1
!
interface Loopback0
ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
description Link to S1
ip dhcp relay information trusted
ip address 192.168.10.1 255.255.255.0
no shutdown
!
line con 0
logging synchronous
exec-timeout 0 0
```

### 2.  Проверьте текущую конфигурацию на R1, используя следующую команду:
  
R1# **show ip interface brief**

```
R1#sh ip int br
Interface IP-Address OK? Method Status Protocol
GigabitEthernet0/0/0 unassigned YES unset administratively down down
GigabitEthernet0/0/1 192.168.10.1 YES manual up up
GigabitEthernet0/0/2 unassigned YES unset administratively down down
Loopback0 10.10.1.1 YES manual up up
Vlan1 unassigned YES unset administratively down down
```

### 3.  Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).
    
### Шаг 1.3.Настройка и проверка основных параметров коммутатора

### 3.  Настройте имя хоста для коммутаторов S1 и S2.

```
Switch(config)#hostname S1
```

```
Switch(config)#hostname S2
```


### 1.  Запретите нежелательный поиск в DNS.

```
S1(config)#no ip domain lookup
```

```
S2(config)#no ip domain lookup
```

### 2.  Настройте описания интерфейса для портов, которые используются в S1 и S2.

```
S1(config)#int f0/5
S1(config-if)#des Link to R1
S1(config-if)#ex
S1(config)#int f0/6
S1(config-if)#des Link to PC-A
S1(config-if)#ex
S1(config)#int f0/1
S1(config-if)#des Link to S2
```    

```
S2(config)#int f0/1
S2(config-if)#des Link to S1
S2(config-if)#ex
S2(config)#int f0/18
S2(config-if)#des Link to PC-B
```

## Часть 2.Настройка сетей VLAN на коммутаторах.

### Шаг 2.1.Сконфигруриуйте VLAN 10.

Добавьте VLAN 10 на S1 и S2 и назовите VLAN - **Management.**

```
S1(config)# vlan 10
S1(config-vlan)#name Management
```

```
S2(config)#vlan 10
S2(config-vlan)#name Management
```

### Шаг 2.2.Сконфигруриуйте SVI для VLAN 10.

Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.

### 1.3.a.Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.

```
S1(config)#int vlan 10
S1(config-if)#ip add 192.168.10.201 255.255.255.0
S1(config-if)#ex
S1(config)#ip default-gateway 192.168.10.1
```

```
S2(config)#int vlan 10
S2(config-if)#ip add 192.168.10.202 255.255.255.0
S2(config-if)#ex
S2(config)#ip default-gateway 192.168.10.1

```

### Шаг 2.3.Настройте VLAN 333 с именем Native на S1 и S2.
### Шаг 2.4.Настройте VLAN 999 с именем ParkingLot на S1 и S2.

```
S1(config)#vlan 333
S1(config-vlan)#name Native
S1(config-vlan)#ex
S1(config)#vlan 999
S1(config-vlan)#name ParkingLot
S1(config-vlan)#ex
```

```
S2(config)#vlan 333
S2(config-vlan)#name Native
S2(config-vlan)#ex
S2(config)#vlan 999
S2(config-vlan)#name ParkingLot
```

## Часть 3.Настройки безопасности коммутатора.

### Шаг 3.1.Релизация магистральных соединений 802.1Q.

3.1.a.Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.

```
S1(config)#int f0/1
S1(config-if)#swi mode tru
S1(config-if)#swi tru native vlan 333
```

```
S2(config)#int f0/1
S2(config-if)#swi mode tru
S2(config-if)#swi tru native vlan 333
```

### 3.1.b.Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.

```
S1#sh int tru
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      333

Port        Vlans allowed on trunk
Fa0/1       1-1005

Port        Vlans allowed and active in management domain
Fa0/1       1,10,333,999

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,333,999
```

```
S2#sh int tru
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      333

Port        Vlans allowed on trunk
Fa0/1       1-1005

Port        Vlans allowed and active in management domain
Fa0/1       1,10,333,999

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,333,999

```

### 3.1.c.Отключить согласование DTP F0/1 на S1 и S2.

```
S1(config)#int f0/1
S1(config-if)#swi non
```

```
S2(config)#int f0/1
S2(config-if)#swi non
```

### 3.1.d.Проверьте с помощью команды **show interfaces**.

```
S1#sh int f0/1 swi | include Negotiation
Negotiation of Trunking: Off
```

```
S2#sh int f0/1 swi | include Negotiation
Negotiation of Trunking: Off
```

### Шаг 3.2.Настройка портов доступа

### 3.2.a.На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.

```
S1(config)#int ra f0/5-6
S1(config-if-range)#swi mode acc
S1(config-if-range)#swi acc vlan 10
```

### 3.2.b.На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.

```
S2(config)#int f0/18
S2(config-if)#swi mode acc
S2(config-if)#swi acc vlan 10
```

### Шаг 3.3.Безопасность неиспользуемых портов коммутатора

### 3.3.a.На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.

```
S1(config)#int ra f0/2-4,f0/7-24,g0/1-2
S1(config-if-range)#swi mode acc
S1(config-if-range)#swi acc vlan 999
S1(config-if-range)#sh
```

```
S2(config)#int ra f0/2-17,f0/19-24,g0/1-2
S2(config-if-range)#swi mode acc
S2(config-if-range)#swi acc vlan 999
S2(config-if-range)#sh
```

### 3.3.b.Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999, введя команду show.

```
S1#sh int sta
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1     Link to S2         connected    trunk      auto    auto  10/100BaseTX
Fa0/2                        disabled 999        auto    auto  10/100BaseTX
Fa0/3                        disabled 999        auto    auto  10/100BaseTX
Fa0/4                        disabled 999        auto    auto  10/100BaseTX
Fa0/5     Link to R1         connected    10         auto    auto  10/100BaseTX
Fa0/6     Link to PC-A       connected    10         auto    auto  10/100BaseTX
Fa0/7                        disabled 999        auto    auto  10/100BaseTX
Fa0/8                        disabled 999        auto    auto  10/100BaseTX
Fa0/9                        disabled 999        auto    auto  10/100BaseTX
Fa0/10                       disabled 999        auto    auto  10/100BaseTX
Fa0/11                       disabled 999        auto    auto  10/100BaseTX
Fa0/12                       disabled 999        auto    auto  10/100BaseTX
Fa0/13                       disabled 999        auto    auto  10/100BaseTX
Fa0/14                       disabled 999        auto    auto  10/100BaseTX
Fa0/15                       disabled 999        auto    auto  10/100BaseTX
Fa0/16                       disabled 999        auto    auto  10/100BaseTX
Fa0/17                       disabled 999        auto    auto  10/100BaseTX
Fa0/18                       disabled 999        auto    auto  10/100BaseTX
Fa0/19                       disabled 999        auto    auto  10/100BaseTX
Fa0/20                       disabled 999        auto    auto  10/100BaseTX
Fa0/21                       disabled 999        auto    auto  10/100BaseTX
Fa0/22                       disabled 999        auto    auto  10/100BaseTX
Fa0/23                       disabled 999        auto    auto  10/100BaseTX
Fa0/24                       disabled 999        auto    auto  10/100BaseTX
Gig0/1                       disabled 999        auto    auto  10/100BaseTX
Gig0/2                       disabled 999        auto    auto  10/100BaseTX
```

```
S2#sh int sta
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1     Link to S1         connected    trunk      auto    auto  10/100BaseTX
Fa0/2                        disabled 999        auto    auto  10/100BaseTX
Fa0/3                        disabled 999        auto    auto  10/100BaseTX
Fa0/4                        disabled 999        auto    auto  10/100BaseTX
Fa0/5                        disabled 999        auto    auto  10/100BaseTX
Fa0/6                        disabled 999        auto    auto  10/100BaseTX
Fa0/7                        disabled 999        auto    auto  10/100BaseTX
Fa0/8                        disabled 999        auto    auto  10/100BaseTX
Fa0/9                        disabled 999        auto    auto  10/100BaseTX
Fa0/10                       disabled 999        auto    auto  10/100BaseTX
Fa0/11                       disabled 999        auto    auto  10/100BaseTX
Fa0/12                       disabled 999        auto    auto  10/100BaseTX
Fa0/13                       disabled 999        auto    auto  10/100BaseTX
Fa0/14                       disabled 999        auto    auto  10/100BaseTX
Fa0/15                       disabled 999        auto    auto  10/100BaseTX
Fa0/16                       disabled 999        auto    auto  10/100BaseTX
Fa0/17                       disabled 999        auto    auto  10/100BaseTX
Fa0/18    Link to PC-B       connected    10         auto    auto  10/100BaseTX
Fa0/19                       disabled 999        auto    auto  10/100BaseTX
Fa0/20                       disabled 999        auto    auto  10/100BaseTX
Fa0/21                       disabled 999        auto    auto  10/100BaseTX
Fa0/22                       disabled 999        auto    auto  10/100BaseTX
Fa0/23                       disabled 999        auto    auto  10/100BaseTX
Fa0/24                       disabled 999        auto    auto  10/100BaseTX
Gig0/1                       disabled 999        auto    auto  10/100BaseTX
Gig0/2                       disabled 999        auto    auto  10/100BaseTX
```

### Шаг 3.4.Документирование и реализация функций безопасности порта.

Интерфейсы F0/6 на S1 и F0/18 на S2 настроены как порты доступа. На этом шаге вы также настроите безопасность портов на этих двух портах доступа.

### 3.4.a.На S1, введите команду **show port-security interface f0/6** для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.

```
S1#sh port-se int f0/6
Port Security              : Disabled
Port Status                : Secure-down
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
```  

|Конфигурация безопасности порта по умолчанию||
|-|-|
|Функция|Настройка по умолчанию|
|Защита портов|Disabled|
|Максимальное количество записей MAC-адресов|1|
|Режим проверки на нарушение безопасности|Shutdown|
|Aging Time|0 мин|
|Aging Type|Absolute|
|Secure Static Address Aging|Disabled|
|Sticky MAC Address|0|

### 3.4.b.На S1 включите защиту порта на F0 / 6 со следующими настройками:
Максимальное количество записей MAC-адресов: **3**
Режим безопасности: **restrict**
Aging time: **60 мин.**
Aging type: **неактивный** - **нет в CPT**

```
S1(config)#int f0/6
S1(config-if)#swi port-s vio restrict
S1(config-if)#swi port-s aging time 60
```

### 3.4.c.Verify port security on S1 F0/6.

```
S1#sh port-s int f0/6
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 60 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0

S1#sh port-s add
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
10	0004.9A1E.7AD1	DynamicConfigured	FastEthernet0/6		-
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
```  

### 3.4.d.Включите безопасность порта для F0 / 18 на S2. Настройте каждый активный порт доступа таким образом, чтобы он автоматически добавлял адреса МАС, изученные на этом порту, в текущую конфигурацию.

### 3.4.e.Настройте следующие параметры безопасности порта на S2 F / 18:
Максимальное количество записей MAC-адресов: **2**
Тип безопасности: **Protect**
Aging time: **60 мин.**

```
S2(config)#int f0/18
S2(config-if)#swi port-s
S2(config-if)#swi port-s vio prot
S2(config-if)#swi port-s agi time 60
```

### 3.4.f.Проверка функции безопасности портов на S2 F0/18.

```
S2#sh port-s int f0/18
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Protect
Aging Time                 : 60 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0

S2#sh port-s add
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
10	0040.0BED.5037	DynamicConfigured	FastEthernet0/18		-
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 0
Max Addresses limit in System (excluding one mac per port) : 1024
```

### Шаг 3.5.Реализовать безопасность DHCP snooping.

### 3.5.a.На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.

```
S2(config)#ip dhcp snooping 
S2(config)#ip dhcp snooping vlan 10
S2(config)#ip arp inspection vlan 10
```

### 3.5.b.Настройте магистральные порты на S2 как доверенные порты.

```
S2(config)#int f0/1
S2(config-if)#ip dhcp snooping trust 
S2(config-if)#ip arp inspection trust 
```

### 3.5.c.Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.

```
S2(config)#int f0/18
S2(config-if)#ip dhcp snooping limit rate 5
```

### 3.5.d.Проверка DHCP Snooping на S2.

```
S2#sh ip dhcp snooping 
Switch DHCP snooping is enabled
DHCP snooping is configured on following VLANs:
10
Insertion of option 82 is disabled
Option 82 on untrusted port is not allowed
Verification of hwaddr field is enabled
Interface                  Trusted    Rate limit (pps)
-----------------------    -------    ----------------
FastEthernet0/1            yes        unlimited       
FastEthernet0/18           no         5               
```

### 3.5.e.В командной строке на PC-B освободите, а затем обновите IP-адрес.

```
C:\>ipconfig /release

   IP Address......................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: 0.0.0.0
   DNS Server......................: 0.0.0.0

C:\>ipconfig /renew

   IP Address......................: 192.168.10.11
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 192.168.10.1
   DNS Server......................: 0.0.0.0
```

### 3.5.f.Проверьте  привязку  отслеживания DHCP с  помощью  команды  **show ip dhcp snooping binding**.

```
S2#sh ip dhcp snooping binding 
MacAddress          IpAddress        Lease(sec)  Type           VLAN  Interface
------------------  ---------------  ----------  -------------  ----  -----------------
00:40:0B:ED:50:37   192.168.10.11    0           dhcp-snooping  10    FastEthernet0/18
Total number of bindings: 1
```

### Шаг 3.6.Реализация PortFast и BPDU Guard

### 3.6.a.Настройте PortFast на всех портах доступа, которые используются на обоих коммутаторах.

```
S1(config)#int ra f0/5-6
S1(config-if-range)#spa portf
```

```
S2(config)#int f0/18
S2(config-if)#spa portfa
```

3.6.b.Включите защиту BPDU на портах доступа VLAN 10 S1 и S2, подключенных к PC-A и PC-B.

```
S1(config)#int f0/6
S1(config-if)# spa bpdu ena
```

```
S2(config)#int f0/18
S2(config-if)#spa bpdu ena
```

### 3.6.c.Убедитесь, что защита BPDU и PortFast включены на соответствующих портах.

***Включено, команда не отображает***
```
sh spa int f0/6 det


Port 6 (FastEthernet0/6) of VLAN0010 is designated forwarding
  Port path cost 19, Port priority 128, Port Identifier 128.6
  Designated root has priority 32778, address 0005.5E19.0092
  Designated bridge has priority 32778, address 0060.3E18.5974
  Designated port id is 128.6, designated path cost 19
  Timers: message age 16, forward delay 0, hold 0
  Number of transitions to forwarding state: 1
  The port is in the portfast mode
  Link type is point-to-point by default
```

```
Port 18 (FastEthernet0/18) of VLAN0010 is designated forwarding
  Port path cost 19, Port priority 128, Port Identifier 128.18
  Designated root has priority 32778, address 0005.5E19.0092
  Designated bridge has priority 32778, address 0005.5E19.0092
  Designated port id is 128.18, designated path cost 19
  Timers: message age 16, forward delay 0, hold 0
  Number of transitions to forwarding state: 1
  The port is in the portfast mode
  Link type is point-to-point by default
```

### Шаг 3.7.Проверьте наличие сквозного ⁪подключения.

Проверьте PING свзяь между всеми устройствами в таблице IP-адресации. В случае сбоя проверки связи может потребоваться отключить брандмауэр на хостах.
***PC-A***
```
C:\>ping 192.168.10.1

Pinging 192.168.10.1 with 32 bytes of data:

Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.10.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms


C:\>ping 192.168.10.11

Pinging 192.168.10.11 with 32 bytes of data:

Reply from 192.168.10.11: bytes=32 time<1ms TTL=128
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128
Reply from 192.168.10.11: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.10.11:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.10.201

Pinging 192.168.10.201 with 32 bytes of data:

Request timed out.
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.10.201:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.10.202

Pinging 192.168.10.202 with 32 bytes of data:

Request timed out.
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.10.202:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

***PC-B***

```
C:\>ping 192.168.10.10

Pinging 192.168.10.10 with 32 bytes of data:

Reply from 192.168.10.10: bytes=32 time<1ms TTL=128
Reply from 192.168.10.10: bytes=32 time<1ms TTL=128
Reply from 192.168.10.10: bytes=32 time<1ms TTL=128
Reply from 192.168.10.10: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.10.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.10.1

Pinging 192.168.10.1 with 32 bytes of data:

Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.10.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.10.201

Pinging 192.168.10.201 with 32 bytes of data:

Request timed out.
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
Reply from 192.168.10.201: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.10.201:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.10.202

Pinging 192.168.10.202 with 32 bytes of data:

Request timed out.
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255
Reply from 192.168.10.202: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.10.202:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

# Вопросы для повторения

### Часть 1.С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах, когда было сконфигурировано динамическое обучение - sticky?
Sticky адреса не стареют, прописываются в конфигурацию.

### Часть 2.Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порту 18 на PC-B никогда не получит IP-адрес через DHCP?
Не очень понимаю вопрос. Если загрузить скриптом текущий конфиг S2 на чистый свитч - не должно подобного произойти, нет sticky адресов. Также отключена ip dhcp snooping information option, т.к. нет поддержки в CPT

### Часть 3.Что касается безопасности порта, в чем разница между типом абсолютного устаревания и типом устаревание по неактивности? 
Устаревание по неактивности в теории может длиться неограниченное время при периодическом появлении устройства в сети. Абсолютное обновится через указанное время после записи адреса.