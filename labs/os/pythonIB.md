# Язык Python. Работа с сетью.
## Задание 1
Просканируйте с помошью Python ВМ Metasploitable. Определите установленные службы (нужно вывести название и версию службы, номер порта.)
#### Ответ:
```
import nmap3


nmap_v = nmap3.Nmap()
def nmap_scan_result(ip):
    version = nmap_v.nmap_version_detection(ip)
    for i in version[ip]['ports']:
        print(f"Service - {i['service']['name']}; Version - {i['service']['version']}; Port - {i['portid']}")

nmap_scan_result(input('Enter IP to scan\n'))
```
## Задание 2* (необязательное)
Данное задание не является обязательным, т.к. может потребоваться дополнительное оборудование.

Попробуйте повторить сканирование Wi-Fi, приведенное в лекции.

Помните, что атаковать чужие устройства - наказуемое законом дeяние. Поэтому, все работы должны проводится на вашем оборудовании или с письменного согласия владельца!

#### Ответ:
```
import pywifi
import time


wifi = pywifi.PyWiFi()
interface = wifi.interfaces()[0]

print(f"Мой интерфейс: {interface.name()}")
interface.scan()
time.sleep(2)

result = interface.scan_results()
print("Доступные сети:")

for i in range(len(result)):
    print(result[i].ssid, result[i].bssid, result[i].auth, result[i].akm)
```
