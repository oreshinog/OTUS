# Лабораторная работа - Настройка протоколов CDP, LLDP и NTP

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab13/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IP-адрес|Маска подсети|Шлюз по умолчанию|
|--|--|--|--|--|
|R1|Loopback1|172.16.1.1|255.255.255.0|--|
||G0/0/1|10.22.0.1|255.255.255.0||
|S1|VLAN 1|10.22.0.2|255.255.255.0|10.22.0.1|
|S2|VLAN 1|10.22.0.3|255.255.255.0|10.22.0.1|

# Задачи

**Часть 1. Создание сети и настройка основных параметров устройства**
**Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP**
**Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP**
**Часть 4. Настройка и проверка NTP**

# Необходимые ресурсы

1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией.

### 1. Создание сети и настройка основных параметров устройства
В первой части лабораторной работы вам предстоит создать топологию сети и настроить основные параметры для маршрутизатора и коммутаторов.
#### Шаг 1.1. Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab13/Img/Img01.png)

#### Шаг 1.2. Настройте базовые параметры для маршрутизатора.
Откройте окно конфигурации
1.2.a. Назначьте маршрутизатору имя устройства.
1.2.b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
1.2.c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
1.2.d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
1.2.e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
1.2.f. Зашифруйте открытые пароли.
1.2.g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
1.2.h. Настройка интерфейсов, перечисленных в таблице выше
1.2.i. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Закройте окно настройки.

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

R1(config)#int g0/0/1
R1(config-if)#ip add 10.22.0.1 255.255.255.0
R1(config-if)#no shu
R1(config-if)#ex
R1(config)#int Loopback1

R1(config-if)#
%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R1(config-if)#ip add 172.16.1.1 255.255.255.0
R1(config-if)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

#### Шаг 1.3. Настройте базовые параметры каждого коммутатора.
Откройте окно конфигурации
1.3.a. Присвойте коммутатору имя устройства.
1.3.b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
1.3.c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
1.3.d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
1.3.e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
1.3.f. Зашифруйте открытые пароли.
1.3.g. Создайте баннер, который предупреждает всех, кто обращается к устройству, видит баннерное сообщение «Только авторизованные пользователи!».  
1.3.h. Отключите неиспользуемые интерфейсы
1.3.i. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Закройте окно настройки.

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

S1(config)#int ra f0/2-4, f0/6-24, g0/1-2
S1(config-if-range)#shu
S1(config-if-range)#end
S1#
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

S2(config)#int ra f0/2-24, g0/1-2
S2(config-if-range)#shu
S2(config-if-range)#end
S2#
%SYS-5-CONFIG_I: Configured from console by console

S2#copy run sta
Destination filename [startup-config]? 
Building configuration...
[OK]
```

#### 2. Обнаружение сетевых ресурсов с помощью протокола CDP
На устройствах Cisco протокол CDP включен по умолчанию. Воспользуйтесь CDP, чтобы обнаружить порты, к которым подключены кабели.
Откройте окно конфигурации
#### 2.1.a. На R1 используйте соответствующую команду show cdp, чтобы определить, сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.

 ```
R1#sh cdp
Global CDP information:
    Sending CDP packets every 60 seconds
    Sending a holdtime value of 180 seconds
    Sending CDPv2 advertisements is enabled
R1#sh cdp interface 
Vlan1 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/0 is administratively down, line protocol is down
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
GigabitEthernet0/0/1 is up, line protocol is up
  Sending CDP packets every 60 seconds
  Holdtime is 180 seconds
```


#### Вопрос: Сколько интерфейсов участвует в объявлениях CDP? Какие из них активны? 
Введите ваш ответ здесь. **Только интерфейс GigabitEthernet0/0/1, остальные отключены**
 
 #### 1.1.a. На R1 используйте соответствующую команду show cdp, чтобы определить версию IOS, используемую на S1.

```
R1#show cdp entry S1

Device ID: S1
Entry address(es): 
Platform: cisco 2960, Capabilities: Switch
Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime: 152

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

advertisement version: 2
Duplex: full
```
#### Вопрос: Какая версия IOS используется на  S1?
Введите ваш ответ здесь. **15.0(2)SE4**
 
1.1.a. На S1 используйте соответствующую команду show cdp, чтобы определить, сколько пакетов CDP было выданных.

```
R1#sh cdp ?
  entry      Information for specific neighbor entry
  interface  CDP interface status and configuration
  neighbors  CDP neighbor entries
  <cr>
```

Вопрос: Сколько пакетов имеет выход CDP с момента последнего сброса счетчика?
Введите ваш ответ здесь. **CPT не поддерживает команду show cdp traffic**
 
#### 1.1.a. Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше. Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов.
 
```
S1(config)#int vlan1
S1(config-if)#ip add 10.22.0.2 255.255.255.0
S1(config-if)#no sh
S1(config-if)#ex
S1(config)#ip def 10.22.0.1
```

```
S2(config)#int vlan1
S2(config-if)#ip add 10.22.0.3 255.255.255.0
S2(config-if)#no sh
S2(config-if)#ex
S2(config)#ip def 10.22.0.1
```

#### 1.1.b. На R1 выполните команду show cdp entry S1 . 

```
R1#show cdp entry S1

Device ID: S1
Entry address(es): 
  IP address : 10.22.0.2
Platform: cisco 2960, Capabilities: Switch
Interface: GigabitEthernet0/0/1, Port ID (outgoing port): FastEthernet0/5
Holdtime: 121

Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

advertisement version: 2
Duplex: full
```

#### Вопрос: Какие дополнительные сведения доступны теперь?
Введите ваш ответ здесь. **Появился IP адрес свитча S1**
 
#### 1.1.a. Отключить CDP глобально на всех устройствах. 
 
```
R1(config)#no cdp run
```

```
S1(config)#no cdp run
```

```
S2(config)#no cdp run
``` 
 
Закройте окно настройки.
### 2. Обнаружение сетевых ресурсов с помощью протокола LLDP
На устройствах Cisco протокол LLDP может быть включен по умолчанию. Воспользуйтесь LLDP, чтобы обнаружить порты, к которым подключены кабели.
Откройте окно конфигурации
#### 2.1.a. Введите соответствующую команду lldp, чтобы включить LLDP на всех устройствах в топологии.

```
R1(config)#lldp run
```

```
S1(config)#lldp run
```

```
S2(config)#lldp run
```

#### 2.1.b. На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2. 

```
S1#sh lldp ?
  neighbors  LLDP neighbor entries
  <cr>
S1#sh lldp neighbors 
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
R1                  Fa0/5          120        R               Gig0/0/1
S2                  Fa0/1          120        B               Fa0/1

Total entries displayed: 2
```
#### Вопрос: Что такое chassis ID  для коммутатора S2?
Введите ваш ответ здесь. **На CPT функционал ограничен, chassis ID должен показывать MAC адрес  интерфейса управления устройства**
 
Закройте окно настройки.
#### 1.1.a. Соединитесь через консоль на всех устройствах и используйте команды LLDP, необходимые для отображения топологии физической сети только из выходных данных команды show.

```
R1#sh lldp nei
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S1                  Gig0/0/1       120        B               Fa0/5

Total entries displayed: 1
```

```
S1#sh lldp nei
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
R1                  Fa0/5          120        R               Gig0/0/1
S2                  Fa0/1          120        B               Fa0/1

Total entries displayed: 2
```

```
S2#sh lldp nei
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S1                  Fa0/1          120        B               Fa0/1

Total entries displayed: 1
``` 

### 2. Настройка NTP
В части 4 необходимо настроить маршрутизатор R1 в качестве сервера NTP, а маршрутизатор R2 в качестве клиента NTP маршрутизатора R1. Необходимо выполнить синхронизацию времени для Syslog и отладочных функций. Если время не синхронизировано, сложно определить, какое сетевое событие стало причиной данного сообщения.

#### Шаг 2.1. Выведите на экран текущее время.
Откройте окно конфигурации
Введите команду show clock для отображения текущего времени на R1. Запишите отображаемые сведения о текущем времени в следующей таблице.

```
R1#sh clock detail 
*0:41:31.683 UTC Mon Mar 1 1993
Time source is hardware calendar
```

|Дата|Время|Часовой пояс|Источник времени|
|--|--|--|--|
|01.03.1993|0:39:24.683|UTC|hardware calendar|

#### Шаг 2.2. Установите время.
С помощью команды clock set установите время на маршрутизаторе R1. Введенное время должно быть в формате UTC. 

```
R1#clock set 12:35:00 16 FEB 2025
``` 

#### Шаг 2.3. Настройте главный сервер NTP.
Настройте R1 в качестве хозяина NTP с уровнем слоя 4.

```
R1(config)#ntp master 4
```
 
#### Шаг 2.4. Настройте клиент NTP.
#### 2.4.a. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. Запишите текущее время,  в следующей таблице.

|#|Дата|Время|Часовой пояс|
|--|--|--|--|
|S1|Mar 1 1993|0:52:36.668|UTC|
|S2|Mar 1 1993|0:56:12.843|UTC|





|S1|Feb 16 2025|12:57:17.974|UTC|
|S2|Feb 16 2025|12:57:16.85|UTC|

#### 2.4.b. Настройте S1 и S2 в качестве клиентов NTP. Используйте соответствующие команды NTP для получения времени от интерфейса G0/0/1 R1, а также для периодического обновления календаря или аппаратных часов коммутатора.

```
S1(config)#ntp server 10.22.0.1
```

```
S2(config)#ntp server 10.22.0.1
```

#### Шаг 2.5. Проверьте настройку NTP.
#### 2.5.a. Используйте соответствующую команду show , чтобы убедиться, что S1 и S2 синхронизированы с R1.

```
S1#sh clock detail 
13:1:3.650 UTC Sun Feb 16 2025
Time source is NTP
```

```
S2#sh clock det
13:1:23.656 UTC Sun Feb 16 2025
Time source is NTP
```

Примечание. Синхронизация метки времени на маршрутизаторе R2 с меткой времени на маршрутизаторе R1 может занять несколько минут.
#### 2.5.b. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время.

|#|Дата|Время|Часовой пояс|
|--|--|--|--|
|S1|Feb 16 2025|13:1:3.650|UTC|
|S2|Feb 16 2025|13:1:23.656|UTC|

####  Вопрос для повторения: Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов? Поясните ответ.

**В первую очередь не следует использовать на интерфейсах маршрутизаторов, смотрящих наружу, во вторую очередь - на всех интерфейсах, на которых установлено конечное оборудования, например ПК пользователей, чтобы не дать возможность получить информацию о сетевом оборудовании третьим лицам**