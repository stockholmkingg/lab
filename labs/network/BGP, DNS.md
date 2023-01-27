# Работа сети Интернет (часть 1): BGP, DNS
## Zoom
На любой Linux-машине (можете использовать Kali) с помощью утилиты dig найдите 5 компаний (доменов), которые скорее всего используют Zoom для своей работы.

В качестве результата необходимо отправить:

1. Описание (своими словами) как вы производили поиск
2. Список из 5 доменов
3. Скриншоты вывода dig для каждого домена

#### Ответы:
1. Производил поиск вручную, брал случайные линки.
2. Список доменов:
- netology.ru
- yahoo.com
- ask.com
- imgur.com
- alibaba.com
3. Скриншоты:

<details><summary>netology.ru</summary>
<p>
    <img src=BGPimages/BGPnetology.png>
</p>
</details>

<details><summary>yahoo.com</summary>
<p>
    <img src=BGPimages/BGPyahoo.png>
</p>
</details>

<details><summary>ask.com</summary>
<p>
    <img src=BGPimages/BGPask.png>
</p>
</details>

<details><summary>imgur.com</summary>
<p>
    <img src=BGPimages/BGPimgur.png>
</p>
</details>

<details><summary>alibaba.com</summary>
<p>
    <img src=BGPimages/BGPalibaba.png>
</p>
</details>

## BGP
Создайте в Cisco Packet Tracer следующую топологию:
![BGPscheme.png](..%2F..%2Fimages%2FBGPscheme.png)
Настройте сетевую схему, используя следующие параметры:

1. Host1:
- IP: 10.1.1.10/24
- Default route : 10.1.1.254
2. R1
- IP gi0/0/0: 10.1.1.254/24
- IP gi0/0/1: 192.168.1.1/30
- BGP AS 65001
3. R2
- IP gi0/0/0: 10.2.2.254/24
- IP gi0/0/1: 192.168.1.2/30
- BGP AS 65002
4. Host2
- IP 10.2.2.10/24
- Default route: 10.2.2.254

Запустите ping между Host1 и Host2 (он должен быть успешен).
