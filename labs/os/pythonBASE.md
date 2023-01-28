# Язык Python. Основы.
## Задание 1
Напишите два скрипта, каждый из которых принимает один параметр и:

1. прибавляет к параметру единицу как строку.

    Например:
```
./python3 test_1.py 5
51
```
#### Ответ:
```
number = input()
print(number + '1')
```
2. прибавляет к параметру единицу как число.

    Например:
```
./python3 test_2.py 5
6
```
#### Ответ:
```
number = int(input())
print(number + 1)
```
## Задание 2
Напишите скрипт, который выводит содержимое каталога и подсчитывает в нём количество файлов.

Например:
```
./test_dir.py
admin_scripts
...
Videos
Total: 22
```
#### Ответ:
```
import os.path

path = input()
number = os.listdir(path)
print(*number, sep='\n')
print('Total:', len(number))
```
## Задание 3
Напишите скрипт, который принимает один параметр и определяет, какой объект передан этим параметром (файл, каталог или не существующий).

Например:
```
./test.py \windows
c:\windows - dir
./test.py c:\pagefile.sys
c:\pagefile.sys - file
user@user:~$ c:\windows1
c:\windows1 - not exist
```
#### Ответ:
```
import os.path

path = input()
file = os.path.isfile(path)
dir = os.path.isdir(path)
exist = os.path.exists(path)
if dir == True:
    print(f"{path} - dir")
if file == True:
    print(f"{path} - file")
if exist != True:
    print(f"{path} - not exists")
```
## Задание 4* (необязательное)
### Легенда
Пользователи в нашей компании начали пересылать друг другу некие "секретные" сообщения. Т.к. доступа к средствам криптографии у них нет, для "шифрования" они используют преобразование строк в формат Base64.
### Задача
Написать скрипт, который:

1. принимает на входе два аргумента. Первый - режим преобразования, второй - строка;
2. если первый параметр равен crypt - преобразует второй параметр в строку Base64;
3. если первый параметр равен decrypt - преобразует второй параметр в текст;
4. если первый параметр равен любой другой строке - выйти из скрипта с ненулевым кодом возврата и сообщить об этом пользователю;
5. если количество параметров скрипта не равно двум - выйти из скрипта с ненулевым кодом возврата выдать сообщение пользователю и завершить работу.

Пример работы:
```
$ ./script.py crypt test
Encrypting...
dGVzdAo=
$ ./script.py decrypt dGVzdAo=
Decrypting...
test
```
#### Ответ:
```
import base64

try:
    procedure, text = input('Enter procedure and your text:').split()
    procedure = procedure.lower()
except:
    print('Use "crypt/decrypt text"')
    exit(1)

if procedure == 'crypt':
    ascii_numbers = text.encode('ascii')
    encoded = base64.b64encode(ascii_numbers)
    encoded_ascii = encoded.decode('ascii')
    print(f"Encrypting...\n{encoded_ascii}")
elif procedure == 'decrypt':
    decoded = base64.b64decode(text)
    decoded_ascii = decoded.decode('ascii')
    print(f"Decrypting...\n{decoded_ascii}")
else:
    print('Use "crypt/decrypt text"')
    exit(1)
```