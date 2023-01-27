# Виртуальные частные сети (VPN)
## OpenVPN
В рамках данной лабораторной работы мы попрактикуемся в использовании самого популярного Open Source решения для организации VPN - OpenVPN.

Первое с чего мы начнём - немного теории. OpenVPN предлагает два виртуальных устройства: TUN (только IP-трафик) и TAP (любой трафик). Соответственно, для приложений всё выглядит так, как будто оно использует не обычный Ethernet-интерфейс, а другой, направляя через него трафик. OpenVPN же "шифрует" данный трафик и перенаправляет его через Ethernet-интерфейс.

Поднимите две виртуальные машины:
1. Ubuntu с Адаптер 1 - NAT и Адаптер 2 - Internal Network (10.0.0.1 - вручную)
2. Kali с Адаптер 1 - NAT и Адаптер 2 - Internal Network (10.0.0.2 - вручную)
3. Удостоверьтесь, что машины видят друг друга по адресам 10.0.0.1 и 10.0.0.2 соответственно (команда ping)
4. Установите на обеих машинах OpenVPN:
5. Дополнительно на Ubuntu установите сервер openssh:
6. А на Kali - mc:

### P2P
P2P
Начнём с режима P2P (Point-to-Point).

#### PlainText
Первое, что мы сделаем, попробуем создать туннель без всяких механизмов шифрования и аутентификации.

Ubuntu
```
sudo openvpn --ifconfig 10.1.0.1 10.1.0.2 --dev tun
```
Kali
```
sudo openvpn --ifconfig 10.1.0.2 10.1.0.1 --dev tun --remote 10.0.0.1
```
В данном случае адреса меняются местами и мы указываем к какому адресу нужно подключиться (режим P2P).

Откройте в Kali Wireshark и выберите интерфейс eth1.

Для тестирования мы будем использовать утилиту netcat (она позволит прослушивать на сервере определённый порт, а с клиента подключаться к этому порту).

Вам нужно и на Ubuntu и на Kali открыть ещё по одному терминалу (или вкладке терминала) и не завершая openvpn проделать остальные команды.

Ubuntu (прослушиваем порт 3000)
```
nc -l 3000
```
Kali (подключаемся через туннель к порту 3000 сервера)
```
nc 10.1.0.1 3000

Передаём любой текст, он будет отображаться на сервере в консоли
```
Удостоверьтесь в Wireshark, что данные передаются в открытом виде (Follow UDP Stream).

Завершите работу openvpn на сервере и на клиенте (Ctrl + C).

#### Shared Key
В этом режиме мы будем использовать один ключ для клиента и сервера.

Ubuntu (генерация ключа):
```
openvpn --genkey --secret vpn.key
cat vpn.key
```
Ключ будет выглядеть следующим образом:
```
#
# 2048 bit OpenVPN static key
#
-----BEGIN OpenVPN Static key V1-----
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
-----END OpenVPN Static key V1-----
```
Теперь нам необходимо безопасно передать ключ с сервера на клиент. Проще всего это сделать, воспользовавшись mc (файловый менеджер). Запустите его, набрав в терминале mc:

Нажмите клавишу F9 после чего Enter, чтобы попасть в выпадающее меню. С помощью стрелок переместитесь на пункт Shell link... и нажмите Enter:

В строке подключения введите ubuntu@10.0.0.1, где ubuntu - это ваш логин на машине с Ubuntu (будет другой, если вы использовали образ с OSBoxes), после чего нажмите Enter:

Согласитесь с подключением (и сохранением отпечатка) к 10.0.0.1 (введите yes после чего нажмите Enter и введите пароль от учётной записи на машине Ubuntu):

Важно: пароль не будет отображаться при вводе в целях безопасности.

Вы попадёте в корневой каталог вашего Ubuntu Server'а, по которому сможете перемещаться с помощью стрелок вверх вниз, заходить в каталоги - с помощью Enter, выходить - с помощью Enter на каталоге ...

Копирование в правую панель (где находится файловая система вашего локального компьютера с Kali) осуществляется с помощью клавиши F5 с подтверждением клавишей Enter:

Ubuntu
```
sudo openvpn --ifconfig 10.1.0.1 10.1.0.2 --dev tun --secret vpn.key
```
Kali
```
sudo openvpn --ifconfig 10.1.0.2 10.1.0.1 --dev tun --remote 10.0.0.1 --secret vpn.key --providers legacy default
```
Ubuntu (прослушиваем порт 3000):
```
nc -l 3000
```
Kali (подключаемся через туннель к порту 3000 сервера):
```
nc 10.1.0.1 3000

Передаём любой текст, он будет отображаться на сервере в консоли
```
Удостоверьтесь в Wireshark, что данные не передаются в открытом виде (Follow UDP Stream).

---
На сервере или на клиенте запустите команду с флагом --verb 3

Например, на Kali - sudo openvpn --ifconfig 10.1.0.2 10.1.0.1 --dev tun --remote 10.0.0.1 --secret vpn.key --verb 3
#### Вопрос:
Какая версия OpenSSL используется?
#### Ответ:
OpenSSL 3.0.4
#### Вопрос:
Какой алгоритм (и с какой длиной ключа) используется для шифрования?
#### Ответ:
BF-CBC 128 bit
#### Вопрос:
Какой алгоритм (и с какой длиной ключа) используется дла HMAC аутентификации?
#### Ответ:
SHA1 160 bit

---
Посмотреть все доступные алгоритмы с помощью команд: 
```
sudo openvpn --show-ciphers 
sudo openvpn --show-digests
```
Указать конкретные с помощью флага --cipher, например:
```
--cipher AES-128-CBC (или просто --cipher AES128) 
```
и --auth, например:
```
--auth SHA256
```
Удостоверьтесь, что после указания иных алгоритмов в логе вывод тоже меняется.

#### Вопрос:
Что будет выведено в консоли сервера 
```
sudo openvpn --ifconfig 10.1.0.1 10.1.0.2 --dev tun --secret vpn.key --cipher AES128 --auth SHA256 --verb 3)
```
Если подключиться с клиента командой:
```
sudo openvpn --ifconfig 10.1.0.2 10.1.0.1 --dev tun --remote 10.0.0.1 --secret vpn.key --cipher AES256 --auth SHA256 --verb 3
```
#### Ответ:
Ошибка с шифрованием.

```
Fri Aug 12 02:54:48 2022 disabling NCP mode (--ncp-disable) because not in P2MP client or server mode
Fri Aug 12 02:54:48 2022 OpenVPN 2.4.7 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Mar 22 2022
Fri Aug 12 02:54:48 2022 library versions: OpenSSL 1.1.1f  31 Mar 2020, LZO 2.10
Fri Aug 12 02:54:48 2022 Outgoing Static Key Encryption: Cipher 'AES-128-CBC' initialized with 128 bit key
Fri Aug 12 02:54:48 2022 Outgoing Static Key Encryption: Using 256 bit message hash 'SHA256' for HMAC authentication
Fri Aug 12 02:54:48 2022 Incoming Static Key Encryption: Cipher 'AES-128-CBC' initialized with 128 bit key
Fri Aug 12 02:54:48 2022 Incoming Static Key Encryption: Using 256 bit message hash 'SHA256' for HMAC authentication
Fri Aug 12 02:54:48 2022 TUN/TAP device tun0 opened
Fri Aug 12 02:54:48 2022 TUN/TAP TX queue length set to 100
Fri Aug 12 02:54:48 2022 /sbin/ip link set dev tun0 up mtu 1500
Fri Aug 12 02:54:48 2022 /sbin/ip addr add dev tun0 local 10.1.0.1 peer 10.1.0.2
Fri Aug 12 02:54:48 2022 Could not determine IPv4/IPv6 protocol. Using AF_INET
Fri Aug 12 02:54:48 2022 Socket Buffers: R=[212992->212992] S=[212992->212992]
Fri Aug 12 02:54:48 2022 UDPv4 link local (bound): [AF_INET][undef]:1194
Fri Aug 12 02:54:48 2022 UDPv4 link remote: [AF_UNSPEC]
Fri Aug 12 02:55:16 2022 Authenticate/Decrypt packet error: cipher final failed
```
Если подключиться с клиента командой:
```
sudo openvpn --ifconfig 10.1.0.2 10.1.0.1 --dev tun --remote 10.0.0.1 --secret vpn.key --cipher AES128 --auth SHA512 --verb 3
```
#### Ответ:
Ошибка с аутентификацией.
```
Fri Aug 12 02:59:14 2022 disabling NCP mode (--ncp-disable) because not in P2MP client or server mode
Fri Aug 12 02:59:14 2022 OpenVPN 2.4.7 x86_64-pc-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Mar 22 2022
Fri Aug 12 02:59:14 2022 library versions: OpenSSL 1.1.1f  31 Mar 2020, LZO 2.10
Fri Aug 12 02:59:14 2022 Outgoing Static Key Encryption: Cipher 'AES-128-CBC' initialized with 128 bit key
Fri Aug 12 02:59:14 2022 Outgoing Static Key Encryption: Using 256 bit message hash 'SHA256' for HMAC authentication
Fri Aug 12 02:59:14 2022 Incoming Static Key Encryption: Cipher 'AES-128-CBC' initialized with 128 bit key
Fri Aug 12 02:59:14 2022 Incoming Static Key Encryption: Using 256 bit message hash 'SHA256' for HMAC authentication
Fri Aug 12 02:59:14 2022 TUN/TAP device tun0 opened
Fri Aug 12 02:59:14 2022 TUN/TAP TX queue length set to 100
Fri Aug 12 02:59:14 2022 /sbin/ip link set dev tun0 up mtu 1500
Fri Aug 12 02:59:14 2022 /sbin/ip addr add dev tun0 local 10.1.0.1 peer 10.1.0.2
Fri Aug 12 02:59:14 2022 Could not determine IPv4/IPv6 protocol. Using AF_INET
Fri Aug 12 02:59:14 2022 Socket Buffers: R=[212992->212992] S=[212992->212992]
Fri Aug 12 02:59:14 2022 UDPv4 link local (bound): [AF_INET][undef]:1194
Fri Aug 12 02:59:14 2022 UDPv4 link remote: [AF_UNSPEC]
Fri Aug 12 03:00:12 2022 Authenticate/Decrypt packet error: packet HMAC authentication failed
```
