# Использование командных оболочек 2. PowerShell
## Задание 1
Напишите два скрипта, каждый из которых принимает один параметр и:

1. прибавляет к параметру единицу как строку.

    Например:
```
./test_1.ps1 5
51
```
#### Ответ:
```
$digit0 = '5'
$digit1 = '1'
echo ($digit0+$digit1)
```
2. прибавляет к параметру единицу как число.

    Например:
```
./test_2.ps1 5
6
```
#### Ответ:
```
$digit0 = 5
$digit1 = 1
echo ($digit0+$digit1)
```
## Задание 2
Напишите скрипт, который выводит содержимое каталога и подсчитывает в нём количество файлов.

Например:
```
./test_dir.ps1
admin_scripts
...
Videos
Total: 22
```
#### Ответ:
```
$names = Get-ChildItem -Path $args[0]
foreach($n in $names)
{
  if($n.Mode -eq "d-----") {Write-Host $n.Name' - dir'}
  else {Write-Host $n.Name - file}
}
Write-Host Total: (Get-ChildItem -Path $args[0] | Measure-Object).Count
```
## Задание 3
Напишите скрипт, который принимает один параметр и определяет, какой объект передан этим параметром (файл, каталог или не существующий).

Например:
```
./test.ps1 c:\windows
c:\windows - dir
./test.ps1 c:\pagefile.sys
c:\pagefile.sys - file
user@user:~$./test.ps1 c:\windows1
c:\windows1 - not exist
```
#### Ответ:
```
$names = Get-Item -Path $args[0], spooler -ErrorAction SilentlyContinue

if($names.Mode -eq "d-----") {Write-Host $names' - dir'}
elseif($names.Mode -eq "-a----") {Write-Host $names - file}
else {Write-Host File not exist}
```