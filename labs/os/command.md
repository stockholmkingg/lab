# Использование командных оболочек 1. Bash
## Задание 1
Напишите два скрипта, каждый из которых принимает один параметр и:

1. первый - прибавляет к параметру единицу как строку.

    Например:
```
    user@user:~$ ./test_1.sh 5
    51
```
#### Ответ:
```
digit0=5
digit1=1
echo $digit0$digit1
```
2. второй - прибавляет к параметру единицу как число.

    Например:
```
    user@user:~$ ./test_2.sh 5
    6
```
#### Ответ:
```
digit0=5
digit1=1
echo $(($digit0+$digit1))
```
## Задание 2
Напишите скрипт, который выводит содержимое каталога и подсчитывает в нём количество файлов.

Например:
```
user@user:~$ ./test_dir.sh
admin_scripts
...
Videos Total: 22
```
#### Ответ:
```
path=$1
for i in $(ls $path)
do
echo $i
done
count=$(ls $path | wc -l)
echo 'Total -' $count
```
## Задание 3
Напишите скрипт, который принимает один параметр и определяет, какой объект передан этим параметром (файл, каталог или не существующий).

Например:
```
user@user:~$ ./test.sh /etc
/etc - dir
user@user:~$ ./test.sh /etc/passwd
/etc/passwd - file
user@user:~$ ./test.sh /etc/passwd1
/etc/passwd1 - not exist
```
#### Ответ:
```
if [ -d $1 ]
then
        echo $1 '- dir'
        exit
fi
if [ -f $1 ]
then
        echo $1 '- file'
else
        echo $1 '- not exist'
fi
```
## Задание 4* (необязательное)
### Легенда
Пользователи в нашей компании начали пересылать друг другу некие "секретные" сообщения. Т.к. доступа к средствам криптографии у них нет, для "шифрования" они используют преобразование строк в формат Base64.
### Задача
Написать скрипт для Bash, который:

1. принимает на входе два аргумента. Первый - режим преобразования, второй - строка;
2. если первый параметр равен <code>crypt</code> - преобразует второй параметр в строку Base64;
3. если первый параметр равен <code>decrypt</code> - преобразует второй параметр в текст;
4. если первый параметр равен любой другой строке - выйти из скрипта с ненулевым кодом возврата и сообщить об этом пользователю;
5. если количество параметров скрипта не равно двум - выйти из скрипта с ненулевым кодом возврата выдать сообщение пользователю и завершить работу.

Пример работы:
```
$ ./script.sh crypt test
Encrypting...
dGVzdAo=
$ ./script.sh decrypt dGVzdAo=
Decrypting...
test
```
#### Ответ:
```
if [ $1 = 'crypt' ]
then
  echo 'Encrypting...'
  echo $2 | base64
  exit
fi
if [ $1 = 'decrypt' ]
then
  echo 'Decrypting...'
  echo $2 | base64 --decode
  exit
fi
if [ $1 != 'crypt' ]
then
  echo 'Error.
Choose correct operation (crypt / decrypt)'
  exit
fi
if [ $1 != 'decrypt' ]
then
  echo 'Error.
Choose correct operation (crypt / decrypt)'
fi
```