# Лабораторная работа - Настройка и проверка расширенных списков контроля доступа.

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab11/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IP-адрес|Маска подсети|Шлюз по  умолчанию|
|--|--|--|--|--|
|R1|G0/0/1||||
||G0/0/1.20|10.20.0.1|255.255.255.0||
||G0/0/1.30|10.30.0.1|255.255.255.0||
||G0/0/1.40|10.40.0.1|255.255.255.0||
||G0/0/1.1000||||
||Loopback1|172.16.1.1|255.255.255.0||
|R2|G0/0/1|10.20.0.4|255.255.255.0||
|S1|VLAN 20|10.20.0.2|255.255.255.0|10.20.0.1|
|S2|VLAN 20|10.20.0.3|255.255.255.0|10.20.0.1|
|PC-A|NIC|10.30.0.10|255.255.255.0|10.30.0.1|
|PC-B|NIC|10.40.0.10|255.255.255.0|10.40.0.1|

## Таблица VLAN
|VLAN|Имя|Назначенный интерфейс|
|-|-|-|
|20|Management|S2: F0/5|
|30|Operations|S1: F0/6|
|40|Sales|S2: F0/18|
|999|ParkingLot|S1: F0/2-4, F0/7-24, G0/1-2|
|||S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2|
|1000|Own||

# Задачи

**Часть 1. Создание сети и настройка основных параметров устройства**
**Часть 2. Настройка и проверка списков расширенного контроля доступа**

# Необходимые ресурсы

2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией

# Инструкции

## Часть 1.Создание сети и настройка основных параметров устройства

### Шаг 1.1.Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab11/Img/Img01.png)

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
Destination filename [startup-config]? 
%SYS-5-CONFIG_I: Configured from console by console

Destination filename [startup-config]? 
Building configuration...
[OK]
```

### Шаг 1.3.Настройте базовые параметры каждого коммутатора.

1.3.a.Присвойте коммутатору имя устройства.
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

## Часть 2.Настройка сетей VLAN на коммутаторах.

### Шаг 2.1.Создайте сети VLAN на коммутаторах.

2.1.a.Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.

```
S1(config)#vlan 20
S1(config-vlan)#name Management
S1(config-vlan)#ex
S1(config)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#ex
S1(config)#vlan 40
S1(config-vlan)#name Sales
S1(config-vlan)#ex
S1(config)#vlan 999
S1(config-vlan)#name ParkingLot
S1(config-vlan)#ex
S1(config)#vlan 1000
S1(config-vlan)#name Own
S1(config-vlan)#ex
```

```
S2(config)#vlan 20
S2(config-vlan)#name Management
S2(config-vlan)#ex
S2(config)#vlan 30
S2(config-vlan)#name Operations
S2(config-vlan)#ex
S2(config)#vlan 40
S2(config-vlan)#name Sales
S2(config-vlan)#ex
S2(config)#vlan 999
S2(config-vlan)#name ParkingLot
S2(config-vlan)#ex
S2(config)#vlan 1000
S2(config-vlan)#name Own
S2(config-vlan)#ex
```





2.1.b.Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.

```
S1(config)#int vlan20
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

S1(config-if)#ip add 10.20.0.2 255.255.255.0
S1(config-if)#ex
S1(config)#ip default-gateway 10.20.0.1
```

```
S2(config)#int vlan20
S2(config-if)#
%LINK-5-CHANGED: Interface Vlan20, changed state to up

S2(config-if)#ip add 10.20.0.3 255.255.255.0
S2(config-if)#ex
S2(config)#ip default-gateway  10.20.0.1
```

2.1.c.Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.

```
S1(config)#int ra f0/2-4, f0/7-24, g0/1-2
S1(config-if-range)#swi mode acc
S1(config-if-range)#swi acc vlan 999
S1(config-if-range)#sh
```

```
S2(config)#int ra f0/2-4, f0/6-17, f0/19-24, g0/1-2
S2(config-if-range)#swi mode acc
S2(config-if-range)#swi acc vlan 999
S2(config-if-range)#sh
```

### Шаг 2.2.Назначьте сети VLAN соответствующим интерфейсам коммутатора.

2.2.a.Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

```
S1(config)#int f0/6
S1(config-if)#swi mode acc
S1(config-if)#swi acc vlan 30
```

```
S2(config)#int f0/18
S2(config-if)#swi mode acc
S2(config-if)#swi acc vlan 40
S2(config-if)#ex
S2(config)#int f0/5
S2(config-if)#swi mode acc
S2(config-if)#swi acc vlan 20
```

2.2.b.Выполните команду **show vlan brief**, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.

```
S1#sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/5
20   Management                       active    
30   Operations                       active    Fa0/6
40   Sales                            active    
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
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
```

```
S2#sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   Management                       active    Fa0/5
30   Operations                       active    
40   Sales                            active    Fa0/18
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 Own                              active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active 
```

## Часть 3.·Настройте транки (магистральные каналы).

### Шаг 3.1.Вручную настройте магистральный интерфейс F0/1.

3.1.a.Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.
3.1.b.В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.
3.1.c.В качестве другой части конфигурации транка укажите, что VLAN 10, 20, 30 и 1000 разрешены в транке.

```
S1(config-if)#swi mode tru

S1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan20, changed state to up

S1(config-if)#swi tru nat vlan 1000
S1(config-if)#
%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on FastEthernet0/1 (1000), with S2 FastEthernet0/1 (1).

S1(config-if)#swi tru all vlan 20,30,40,1000
```

```
S2(config)#int f0/1
S2(config-if)#swi mode tru
S2(config-if)#swi tru nat vlan 1000
S2(config-if)#%SPANTREE-2-UNBLOCK_CONSIST_PORT: Unblocking FastEthernet0/1 on VLAN1000. Port consistency restored.

%SPANTREE-2-UNBLOCK_CONSIST_PORT: Unblocking FastEthernet0/1 on VLAN0001. Port consistency restored.


S2(config-if)#swi tru all vlan 20,30,40,1000
```

3.1.d.Выполните команду **show interfaces trunk** для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.

```
S1#sh int tru
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```

```
S2#sh int tru
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```  

### Шаг 3.2.Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

3.2.a.Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
3.2.b.Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1(config)#int f0/5
S1(config-if)#swi mode tru
S1(config-if)#swi tru nat vlan 1000
S1(config-if)#swi tru all vlan 20,30,40,1000
S1(config-if)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

3.2.c.Используйте команду **show interfaces trunk** для проверки настроек транка.

**нет  информации о транке, т.к. порт g0/0/1 на R1 ещё не включен**
```
S1#sh int tru
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30,40,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30,40,1000
```

## Часть 4.Настройте маршрутизацию.

### Шаг 4.1.Настройка маршрутизации между сетями VLAN на R1.

4.1.a.Активируйте интерфейс G0/0/1 на маршрутизаторе.
4.1.b.Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.
4.1.c.Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.

```
R1(config)#int g0/0/1
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R1(config-if)#ex
R1(config)#int g0/0/1.20
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.20, changed state to up

R1(config-subif)#encap dot1q 20
R1(config-subif)#ip add 10.20.0.1 255.255.255.0
R1(config-subif)#des Management
R1(config-subif)#ex
R1(config)#int g0/0/1.30
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.30, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.30, changed state to up

R1(config-subif)#encap dot1q 30
R1(config-subif)#ip add 10.30.0.1 255.255.255.0
R1(config-subif)#des Operations
R1(config-subif)#ex
R1(config)#int g0/0/1.40
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.40, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.40, changed state to up

R1(config-subif)#encap dot1q 40
R1(config-subif)#ip add 10.40.0.1 255.255.255.0
R1(config-subif)#des Sales
R1(config-subif)#ex
R1(config)#int g0/0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up

R1(config-subif)#encap dot1q 1000 nat
R1(config-subif)#ex
R1(config)#int loopback1

R1(config-if)#
%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R1(config-if)#ip add 172.16.1.1 255.255.255.0
```

4.1.d.С помощью команды **show ip interface brief** проверьте конфигурацию подинтерфейса.

```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.2010.20.0.1       YES manual up                    up 
GigabitEthernet0/0/1.3010.30.0.1       YES manual up                    up 
GigabitEthernet0/0/1.4010.40.0.1       YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Loopback1              172.16.1.1      YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
```

### Шаг 4.2.Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1

```
R2(config)#int g0/0/1
R2(config-if)#ip add 10.20.0.4 255.255.255.0
R2(config-if)#no sh

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up

R2(config-if)#ex
R2(config)#ip default-gateway 10.20.0.1
```

## Часть 5.Настройте удаленный доступ

### Шаг 5.1.Настройте все сетевые устройства для базовой поддержки SSH.

5.1.a.Создайте локального пользователя с именем пользователя SSHadmin и зашифрованным паролем $cisco123!
5.1.b.Используйте **ccna-lab.com** в качестве доменного имени.
5.1.c.Генерируйте криптоключи с помощью 1024 битного модуля.
5.1.d.Настройте первые пять линий VTY на каждом устройстве, чтобы поддерживать только SSH-соединения и с локальной аутентификацией.

```
R1(config)#ip domain-name ccna-lab.com
R1(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: R1.ccna-lab.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:4:32.222: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#username SSHadmin secret $cisco123!
R1(config)#line vty 0 4
R1(config-line)#login local
R1(config-line)#transport input ssh
```

```
R2(config)#ip domain-name ccna-lab.com
R2(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: R2.ccna-lab.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:5:11.47: %SSH-5-ENABLED: SSH 1.99 has been enabled
R2(config)#username SSHadmin secret $cisco123!
R2(config)#line vty 0 4
R2(config-line)#login local
R2(config-line)#transport input ssh
```

```
S1(config)#ip domain-name ccna-lab.com
S1(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: S1.ccna-lab.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:5:33.628: %SSH-5-ENABLED: SSH 1.99 has been enabled
S1(config)#username SSHadmin secret $cisco123!
S1(config)#line vty 0 4
S1(config-line)#login local
S1(config-line)#transport input ssh
```

```
S2(config)#ip domain-name ccna-lab.com
S2(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: S2.ccna-lab.com

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:5:56.132: %SSH-5-ENABLED: SSH 1.99 has been enabled
S2(config)#username SSHadmin secret $cisco123!
S2(config)#line vty 0 4
S2(config-line)#login local
S2(config-line)#transport input ssh
```

### Шаг 5.2.Включите защищенные веб-службы с проверкой подлинности на R1.

5.2.a.Включите сервер HTTPS на R1.

**НЕ ПОДДЕРЖИВАЕТСЯ В CPT**

5.2.b.Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.

**НЕ ПОДДЕРЖИВАЕТСЯ В CPT**

## Часть 6.Проверка подключения

### Шаг 6.1.Настройте узлы ПК.

![PC-A1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab11/Img/Img02.png)

![PC-A2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab11/Img/Img03.png)

![PC-B1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab11/Img/Img04.png)

![PC-B2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab11/Img/Img05.png)

### Шаг 6.2.Выполните следующие тесты. Эхозапрос должен пройти успешно.

**Примечание.** Возможно, вам придется отключить брандмауэр ПК для работы ping

|От|Протокол|Назначение|Результат|
|--|--|--|--|
|PC-A|Ping|10.40.0.10|Успех|
|PC-A|Ping|10.20.0.1|Успех|
|PC-B|Ping|10.30.0.10|Успех|
|PC-B|Ping|10.20.0.1|Успех|
|PC-B|Ping|172.16.1.1|Успех|
|PC-B|HTTPS|10.20.0.1|Нет возможности проверить в CPT|
|PC-B|HTTPS|172.16.1.1|Нет возможности проверить в CPT|
|PC-B|SSH|10.20.0.1|Успех|
|PC-B|SSH|172.16.1.1|Успех|

## Часть 7.Настройка и проверка списков контроля доступа (ACL)

При проверке базового подключения компания требует реализации следующих политик безопасности:

**Политика 1**. Сеть Sales не может использовать SSH в сети Management (но в другие сети SSH разрешен).

**Политика 2**. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales может получить доступ к интерфейсу Loopback 1 на R1).

**Политика 3**. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам.

**Политика 4**: Cеть Operations не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам.

### Шаг 7.1.Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.

### Шаг 7.2.Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.

```
R1(config)#ip access-list extended Sales
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq www
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.30.0.1 eq www
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.30.0.1 eq 443
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.40.0.1 eq www
R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 host 10.40.0.1 eq 443
R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255
R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#ex
R1(config)#ip access-list extended Operations
R1(config-ext-nacl)#deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#ex
R1(config)#int g0/0/1.40
R1(config-subif)#ip access-group Sales in
R1(config-subif)#ex
R1(config)#int g0/0/1.40
R1(config-subif)#ip access-group Operations in
```

### Шаг 7.3.Убедитесь, что политики безопасности применяются развернутыми списками доступа.

Выполните следующие тесты. Ожидаемые результаты показаны в таблице:

|От|Протокол|Назначение|Результат|
|--|--|--|--|
|PC-A|Ping|10.40.0.10|Неудача|
|PC-A|Ping|10.20.0.1|Успех|
|PC-B|Ping|10.30.0.10|Неудача|
|PC-B|Ping|10.20.0.1|Неудача|
|PC-B|Ping|172.16.1.1|Успех|
|PC-B|HTTPS|10.20.0.1|Нет возможности проверить в CPT|
|PC-B|HTTPS|172.16.1.1|Нет возможности проверить в CPT|
|PC-B|SSH|10.20.0.1|Неудача|
|PC-B|SSH|172.16.1.1|Успех|