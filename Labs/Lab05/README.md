
# Лабораторная работа - Настройка IPv6-адресов на сетевых устройствах
![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img000.png)
# Таблица адресации
  

|Устройство|Интерфейс|IP-адрес|Маска подсети|Шлюз по умолчанию|
|--|--|--|--|--|
|R1|G0/0/1|192.168.1.1|255.255.255.0|—|
|S1|VLAN 1|192.168.1.11|255.255.255.0|192.168.1.1|
|PC-A|NIC|192.168.1.3|255.255.255.0|192.168.1.1|

## Задачи
**Часть 1. Настройка основных параметров устройства**
**Часть 2. Настройка маршрутизатора для доступа по протоколу SSH**
**Часть 3. Настройка коммутатора для доступа по протоколу SSH**
**Часть 4. SSH через интерфейс командной строки (CLI) коммутатора**

## Общие сведения/сценарий
В этой лабораторной работе вы будете настраивать хосты и интерфейсы устройств с IPv6-адресами. Для просмотра индивидуальных и групповых IPv6-адресов вы будете использовать команду **show**. Вы также будете проверять сквозное соединение с помощью команд **ping** and **traceroute**.

## Необходимые ресурсы
1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
Консольные кабели для настройки устройств Cisco IOS через консольные порты.
Кабели Ethernet, расположенные в соответствии с топологией
## Инструкции

## Часть 1.Настройка основных параметров устройств
В части 1 потребуется настроить топологию сети и основные параметры, такие как IP-адреса интерфейсов, доступ к устройствам и пароли на маршрутизаторе.
### Шаг 1.1.Создайте сеть согласно топологии.

![Топология](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img00.png)

### Шаг 1.2.Выполните инициализацию и перезагрузку маршрутизатора и коммутатора.

### Шаг 1.3.Настройте маршрутизатор.
Откройте окно конфигурации
1.3.a.Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
```
Router>enable
Router#
```
1.3.b.Войдите в режим конфигурации.
```
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#hostname R1
R1(config)#
```
1.3.c.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
R1(config)#no ip domain lookup
```
1.3.d.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
```
R1(config)#enable secret class
```
1.3.e.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
```
R1(config)#line con 0
R1(config-line)#pass cisco
R1(config-line)#login
```
1.3.f.Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
```
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 5 15
R1(config-line)#pass cisco
R1(config-line)#login
```
1.3.g.Зашифруйте открытые пароли.
```
R1(config)#service password-encryption
```
1.3.h.Создайте баннер, который предупреждает о запрете несанкционированного доступа.
```
R1(config)#banner motd #
Unauthorized access is strictly prohibited. #
```
1.3.i.Настройте и активируйте на маршрутизаторе интерфейс G0/0/1, используя информацию, приведенную в таблице адресации.
```
R1(config)#int gi 0/0/1
R1(config-if)#ip add 192.168.1.1 255.255.255.0
R1(config-if)#no shu
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
```
1.3.j.Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]
```
### Шаг 1.4.Настройте компьютер PC-A.
1.4.a.Настройте для PC-A IP-адрес и маску подсети.

![PC-A_IP](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img01.png)

1.4.b.Настройте для PC-A шлюз по умолчанию.

![PC-A_GW](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img02.png)

### Шаг 1.5.Проверьте подключение к сети.

Пошлите с PC-A команду Ping на маршрутизатор R1. Если эхо-запрос с помощью команды ping не проходит, найдите и устраните неполадки подключения.
```
Cisco Packet Tracer PC Command Line 1.0
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.1:
	Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
	Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```
Закройте окно настройки.

## Часть 2.Настройка маршрутизатора для доступа по протоколу SSH

Подключение к сетевым устройствам по протоколу Telnet сопряжено с риском для безопасности, поскольку вся информация передается в виде открытого текста. Протокол SSH шифрует данные сеанса и обеспечивает аутентификацию устройств, поэтому для удаленных подключений рекомендуется использовать именно этот протокол. В части 2 вам нужно настроить маршрутизатор для приема соединений SSH по линиям VTY.

### Шаг 2.1.Настройте аутентификацию устройств.

При генерации ключа шифрования в качестве его части используются имя устройства и домен. Поэтому эти имена необходимо указать перед вводом команды **crypto key**.

Откройте окно конфигурации

2.1.a.Задайте имя устройства.
```
Router(config)#hostname R1
R1(config)#
```
2.1.b.Задайте домен для устройства.
```
R1(config)#ip domain-name cisco.local
```
### Шаг 2.2.Создайте ключ шифрования с указанием его длины.
```
R1(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: R1.cisco.local

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 0:43:28.872: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#
```

### Шаг 2.3.Создайте имя пользователя в локальной базе учетных записей.

Настройте имя пользователя, используя **admin** в качестве имени пользователя и **Adm1nP@55** в качестве пароля.
```
R1(config)#username admin secret Adm1nP@55
```

### Шаг 2.4.Активируйте протокол SSH на линиях VTY.

2.4.a.Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды **transport input**.
2.4.b.Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.
```
R1(config)#line vty 0 4
R1(config-line)#login local
R1(config-line)#transport input ssh
R1(config-line)#exit
R1(config)#line vty 5 15
R1(config-line)#login local
R1(config-line)#transport input ssh
```
### Шаг 2.5.Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config
Destination filename [startup-config]?
Building configuration...
[OK]
```

### Шаг 2.6.Установите соединение с маршрутизатором по протоколу SSH.

2.6.a.Запустите Tera Term с PC-A.

2.6.b.Установите SSH-подключение к R1. Use the username **admin** and password **Adm1nP@55**. У вас должно получиться установить SSH-подключение к R1.

![PC-A_SSH1](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img03.png)
  
![PC-A_SSH2](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img04.png)

Закройте окно настройки.

## Часть 3.Настройка коммутатора для доступа по протоколу SSH

В части 3 вам предстоит настроить коммутатор для приема подключений по протоколу SSH, а затем установить SSH-подключение с помощью программы Tera Term.

### Шаг 3.1.Настройте основные параметры коммутатора.

Откройте окно конфигурации

3.1.a.Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.
```
Switch>enable
```
3.1.b.Войдите в режим конфигурации.
```
Switch#conf t
```
3.1.c.Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
Switch(config)#no ip domain lookup
```
3.1.d.Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
```
Switch(config)#enable secret class
```
3.1.e.Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
```
Switch(config)#line con 0
Switch(config-line)#pass cisco
Switch(config-line)#login
```
3.1.f.Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
```
Switch(config)#line vty 0 4
Switch(config-line)#pass cisco
Switch(config-line)#login
Switch(config-line)#exit
Switch(config)#line vty 5 15
Switch(config-line)#pass cisco
Switch(config-line)#login
```
3.1.g.Зашифруйте открытые пароли.
```
Switch(config)#service password-encryption
```
3.1.h.Создайте баннер, который предупреждает о запрете несанкционированного доступа.
```
Switch(config)#banner motd #
Enter TEXT message. End with the character '#'.
Unauthorized access is strictly prohibited. #
```
3.1.i.Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.
```
Switch(config)#int vlan1
Switch(config-if)#ip address 192.168.1.11 255.255.255.0
Switch(config-if)#no shu
Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
Switch(config-if)#exi
Switch(config)#ip default-gateway 192.168.1.1
```
3.1.j.Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
Switch#copy run sta
Destination filename [startup-config]?
Building configuration...
[OK]
```

### Шаг 3.2.Настройте коммутатор для соединения по протоколу SSH.

Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.

3.2.a.Настройте имя устройства, как указано в таблице адресации.
```
Switch(config)#hostname S1
S1(config)#
```
3.2.b.Задайте домен для устройства.
```
S1(config)#ip domain-name cisco.local
```
3.2.c.Создайте ключ шифрования с указанием его длины.
```
S1(config)#crypto key generate rsa general-keys modulus 1024
The name for the keys will be: S1.cisco.local

% The key modulus size is 1024 bits
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
*Mar 1 1:18:1.15: %SSH-5-ENABLED: SSH 1.99 has been enabled
```
3.2.d.Создайте имя пользователя в локальной базе учетных записей.
```
S1(config)#username admin secret Adm1nP@55
```
3.2.e.Активируйте протоколы Telnet и SSH на линиях VTY.
3.2.f.Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.
```
S1(config)#line vty 0 4

S1(config-line)#login local
S1(config-line)#transport input ssh
S1(config-line)#exit
S1(config)#line vty 5 15
S1(config-line)#login local
S1(config-line)#transport input ssh
```

### Шаг 3.3.Установите соединение с коммутатором по протоколу SSH.

Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1.

![PC-A_SSH3](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img05.png)
  
![PC-A_SSH4](https://github.com/oreshinog/OTUS/blob/main/Labs/Lab05/Img/img06.png)

#### Вопрос:

Удалось ли вам установить SSH-соединение с коммутатором?

 #### Ответ - да
 
Закройте окно настройки.

## Часть 1.Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора

Клиент SSH встроен в операционную систему Cisco IOS и может запускаться из интерфейса командной строки. В части 4 вам предстоит установить соединение с маршрутизатором по протоколу SSH, используя интерфейс командной строки коммутатора.

 ### Шаг 1.1.Посмотрите доступные параметры для клиента SSH в Cisco IOS.

Откройте окно конфигурации

Используйте вопросительный знак (**?**), чтобы отобразить варианты параметров для команды **ssh**.

S1# **ssh?**

-c Select encryption algorithm

-l Log in using this user name

-m Select HMAC algorithm

-o Specify options

-p Connect to this port

-v Specify SSH Protocol Version

-vrf Specify vrf name

WORD IP-адрес или имя хоста удаленной системы

### Шаг 1.2.Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.

1.2.a.Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду **–l admin**. Это позволит вам войти в систему под именем **admin**. При появлении приглашения введите в качестве пароля **Adm1nP@55**

```
S1#ssh -l admin 192.168.1.1

Password:

  
Unauthorized access is strictly prohibited.

R1>
```
1.2.b.Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш **Ctrl**+**Shift**+**6**. Отпустите клавиши **Ctrl**+**Shift**+**6** и нажмите **x**. Отображается приглашение привилегированного режима EXEC коммутатора.

```
S1#ssh -l admin 192.168.1.1

Password:

  
Unauthorized access is strictly prohibited.

R1>
S1#
```
1.2.c.Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.
```
S1#ssh -l admin 192.168.1.1

Password:

  
Unauthorized access is strictly prohibited.

R1>
S1#
S1#
[Resuming connection 1 to 192.168.1.1 ... ]

R1>
```

1.2.d.Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду **exit**.
```
R1>exit

[Connection to 192.168.1.1 closed by foreign host]
S1#
```

#### Вопрос:

Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки?
```
S1#ssh -v ?
1 Protocol Version 1
2 Protocol Version 2
```
Закройте окно настройки.

# Вопрос для повторения

Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?

#### Ответ - создать несколько пользователей и включить доступ по локальной базе пользователей (login local)