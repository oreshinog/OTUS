# Лабораторная работа - Развертывание коммутируемой сети с резервными каналами

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab07/Img/Img00.png)

## Таблица адресации

|Устройство|Интерфейс|IP-адрес|Маска подсети|
|--|--|--|--|
|S1|VLAN 1|192.168.1.1|255.255.255.0|
|S2|VLAN 1|192.168.1.2|255.255.255.0|
|S3|VLAN 1|192.168.1.3|255.255.255.0|

# Цели

**Часть 1. Создание сети и настройка основных параметров устройства**
**Часть 2. Выбор корневого моста**
**Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов**
**Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов**

## Необходимые ресурсы

3 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель)
Консольные кабели для настройки устройств Cisco IOS через консольные порты
Кабели Ethernet, расположенные в соответствии с топологией

## Создание сети и настройка основных параметров устройства

В части 1 вам предстоит настроить топологию сети и основные параметры маршрутизаторов.

### Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![Топология1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab07/Img/Img01.png)

### Выполните инициализацию и перезагрузку коммутаторов.
### Настройте базовые параметры каждого коммутатора.

Отключите поиск DNS.
Присвойте имена устройствам в соответствии с топологией.
Назначьте **class** в качестве зашифрованного пароля доступа к привилегированному режиму.
Назначьте **cisco** в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
Настройте logging synchronous для консольного канала.
Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
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
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #

S1(config)#int vlan1
S1(config-if)#ip add 192.168.1.1 255.255.255.0
S1(config-if)#no shu
S1(config-if)#exi
S1(config)#exi
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
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #

S2(config)#int vlan1
S2(config-if)#ip add 192.168.1.2 255.255.255.0
S2(config-if)#no shu
S2(config-if)#exi
S2(config)#exi
S2#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#hostname S3
S3(config)#no ip domain lookup
S3(config)#enable secret class
S3(config)#line con 0
S3(config-line)#pass cisco
S3(config-line)#logging synchronous
S3(config-line)#login
S3(config-line)#exit
S3(config)#line vty 0 4
S3(config-line)#pass cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#line vty 5 15
S3(config-line)#pass cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#service password-encryption
S3(config)#banner motd #
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #

S3(config)#int vlan1
S3(config-if)#ip add 192.168.1.3 255.255.255.0
S3(config-if)#no shu
S3(config-if)#exi
S3(config)#exi
S3#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```


### Проверьте связь.

Проверьте способность компьютеров обмениваться эхо-запросами.

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2? **Да**

```
S1#ping 192.168.1.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 0/1/3 ms
```

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3? **Да**

```
S1#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 0/0/0 ms
```

Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3? **Да**

```
S2#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 0/0/0 ms
```

Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными.

### Определение корневого моста

Для каждого экземпляра протокола spanning-tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола spanning-tree, позволяя определить избыточные пути, которые следует заблокировать.

Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.

### Отключите все порты на коммутаторах.

```
S1(config)#int range fa 0/1-24
S1(config-if-range)#shu
S1(config-if-range)#exi
S1(config)#int range gi 0/1-2
S1(config-if-range)#shu
```

```
S2(config)#int range fa 0/1-24
S2(config-if-range)#shu
S2(config-if-range)#exi
S2(config)#int range gi 0/1-2
S2(config-if-range)#shu
```

```
S3(config)#int range fa 0/1-24
S3(config-if-range)#shu
S3(config-if-range)#exi
S3(config)#int range gi 0/1-2
S3(config-if-range)#shu
```

### Настройте подключенные порты в качестве транковых.
### Включите порты F0/2 и F0/4 на всех коммутаторах.

```
S1(config)#int ra fa 0/1-4
S1(config-if-range)#swi mode tr
S1(config-if-range)#ex
S1(config)#int ra fa0/2,fa0/4
S1(config-if-range)#no shu
```

```
S2(config)#int ra fa 0/1-4
S2(config-if-range)#swi mode tr
S2(config-if-range)#ex
S2(config)#int ra fa0/2,fa0/4
S2(config-if-range)#no shu
```

```
S3(config)#int ra fa 0/1-4
S3(config-if-range)#swi mode tr
S3(config-if-range)#ex
S3(config)#int ra fa0/2,fa0/4
S3(config-if-range)#no shu
```

### Отобразите данные протокола spanning-tree.

Введите команду **show spanning-tree** на всех трех коммутаторах. Приоритет идентификатора моста рассчитывается путем сложения значений приоритета и расширенного идентификатора системы. Расширенным идентификатором системы всегда является номер сети VLAN. В примере ниже все три коммутатора имеют равные значения приоритета идентификатора моста (32769 = 32768 + 1, где приоритет по умолчанию = 32768, номер сети VLAN = 1); следовательно, коммутатор с самым низким значением MAC-адреса становится корневым мостом (в примере — S2).

```
S1#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000B.BE5E.8CCC
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00E0.F9E1.D7D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p
```

```
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000C.CFD1.1BB5
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```

  

**Примечание**. Режим STP по умолчанию на коммутаторе 2960 — протокол STP для каждой сети VLAN (PVST).

В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.
С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.
Какой коммутатор является корневым мостом?  **S1**

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
При равном значении параметров priority, у него самый наименьший mac адрес
_______________________________________________________________________________________
000B.BE5E.8CCC (S1) < 000C.CFD1.1BB5 (S3) < 00E0.F9E1.D7D9 (S2)
_______________________________________________________________________________________

Какие порты на коммутаторе являются корневыми портами?  **Порты c наименьшей стоимостью пути до корневого моста, на корневом мосте не может быть корневых портов, на устройстве может быть одновременно только один корневой порт**

Какие порты на коммутаторе являются назначенными портами?  **Порты с наименьшей стоимостью пути до сегмента сети**

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? **порт с большей стоимостью пути, чем назначенный порт(или корневой порт) до одного сегмента сети, в данный момент заблокирован порт на S2 - fa0/4**

Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
** S2 fa0/4 заблокирован, т.к. стоимость пути  **

**Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов**

Алгоритм протокола spanning-tree (STA) использует корневой мост как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. Порт с более низкой стоимостью пути является предпочтительным. Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными. В части 3 вам предстоит изменить стоимость порта, чтобы определить, какой порт будет заблокирован протоколом spanning-tree.

**Определите коммутатор с заблокированным портом.** - **это коммутатор S2, порт fa0/4**

```
S2#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00E0.F9E1.D7D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p
```

```
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000C.CFD1.1BB5
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```

### Измените стоимость порта.

Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста. Уменьшите стоимость этого порта корневого моста до 18, выполнив команду **spanning-tree** **vlan** **1 cost 18** режима конфигурации интерфейса.

```
S2(config)#int fa0/2
S2(config-if)#spa vlan 1 cost 18
S2(config-if)#
```

### Просмотрите изменения протокола spanning-tree.

Повторно выполните команду **show spanning-tree** на обоих коммутаторах некорневого моста. Обратите внимание, что ранее заблокированный порт (S1 – F0/4) теперь является назначенным портом, и протокол spanning-tree теперь блокирует порт на другом коммутаторе некорневого моста (S3 – F0/4).

```
S1#sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000B.BE5E.8CCC
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

```
S2#sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        18
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00E0.F9E1.D7D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 18        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

```
S3>sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000C.CFD1.1BB5
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```
Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?
** Приоритет при посылке BPDU рассчитывается в таком порядке - меньший Path cost>меньший Bridge ID>меньший Port ID. До изменения стоимость пути была одинаковой (19+19), Bridge ID у S3<S2, блокировался порт на S2. После изменения, стоимость пути стала меньше, S1-S3-S2(19+19)> S1-S2-S3(19+18), посылка BPDU пошла через S2, соответственно заблокировался порт на S3**

### Удалите изменения стоимости порта.

Выполните команду **no spanning-tree** **vlan** **1 cost 18** режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.

```
S2(config)#int fa0/2
S2(config-if)#no spa vlan 1 cost 18
S2(config-if)#ex
```

Повторно выполните команду **show spanning-tree**, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.

```
S2#sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00E0.F9E1.D7D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p
```

**Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов**

Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Значение приоритета по умолчанию — 128. STP объединяет приоритет порта с номером порта, чтобы разорвать связи. Наиболее низкие значения являются предпочтительными. В части 4 вам предстоит активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.

Включите порты F0/1 и F0/3 на всех коммутаторах.

```
S1(config)#int ra fa0/1,fa0/3
S1(config-if-range)#no shu
```

```
S2(config)#int ra fa0/1,fa0/3
S2(config-if-range)#no shu
```

```
S3(config)#int ra fa0/1,fa0/3
S3(config-if-range)#no shu
```

Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду **show spanning-tree** на коммутаторах некорневого моста. Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

```
S1#sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000B.BE5E.8CCC
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Desg FWD 19        128.1    P2p
Fa0/3            Desg FWD 19        128.3    P2p
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

```
S2#sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        1(FastEthernet0/1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00E0.F9E1.D7D9
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/3            Altn BLK 19        128.3    P2p
Fa0/1            Root FWD 19        128.1    P2p
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p
```

```
S3#sh spa
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000B.BE5E.8CCC
             Cost        19
             Port        3(FastEthernet0/3)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000C.CFD1.1BB5
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/1            Desg FWD 19        128.1    P2p
Fa0/4            Altn BLK 19        128.4    P2p
Fa0/3            Root FWD 19        128.3    P2p
```

Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста? **S2 - fa0/1, S3 - fa/03**

Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?
** приоритет выбора = наименьшая стоимость пути>наименьший Bridge ID посылающего>наименьший Port ID (приоритет порта + номер порта)**

**Вопросы для повторения**

1.Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта? **наименьшая стоимость пути**

2.Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта? **наименьший Bridge ID посылающего**

3.Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта? **наименьший Port ID (приоритет порта + номер порта)**