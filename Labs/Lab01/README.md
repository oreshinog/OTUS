**Лабораторная работа. Базовая настройка коммутатора**

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
![](https://github.com/oreshinog/Lab/Lab01/Img/img1.png)
1.1.b.Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.
![](https://github.com/oreshinog/Lab/Lab01/Img/img2.png)

#### Вопрос:
Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?
#### Ответ: 
Потому что на данном этапе коммутатор ещё не имеет айпи адреса.

### Шаг 1.1.Проверьте настройки коммутатора по умолчанию.
1.1.a.Предположим, что коммутатор не имеет файла конфигурации, сохраненного в энергонезависимой памяти (NVRAM). Консольное подключение к коммутатору с помощью Tera Term или другой программы эмуляции терминала предоставит доступ к командной строке пользовательского режима EXEC в виде Switch>. Введите команду **enable**, чтобы войти в привилегированный режим EXEC.
![](https://github.com/oreshinog/Lab/Lab01/Img/img3.png)
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
![](https://github.com/oreshinog/Lab/Lab01/Img/img5.png)
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





