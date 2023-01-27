# Работа сети Интернет (часть 2): HTTP, HTTPS, HTTP2
## nginx
В этом задании мы посмотрим, как настраивается самый популярный HTTP-сервер - nginx, сгенерируем и установим туда самоподписанный сертификат (это типичная задача внутри организаций - нужно обеспечить защищённый канал, но покупать сертификат для целей разработки или внутреннего обмена желания нет).

Вам потребуются:

1. Виртуальная машина с Ubuntu: Адаптер 1 - NAT и Адаптер 2 - Internal Network (10.0.0.1 - вручную)
2. Виртуальная машина с Kali: Адаптер 1 - NAT и Адаптер 2 - Internal Network (10.0.0.2 - вручную)
3. Виртуальная машина с Windows: Адаптер 1 - NAT и Адаптер 2 - Internal Network (10.0.0.3 - вручную)

### Установка, настройка nginx

Установка пакетов

Все действия из этого раздела выполняются на машине с Ubuntu.

Откройте терминал и выполните следующие команды для установки необходимых пакетов:
```
sudo apt update
sudo apt install nginx openssl mc vim
```
Поcле установки nginx автоматически запустится на 80-м порту. Нас будут интересовать HTTP (80) и HTTPS (443). Откройте их на файерволле, если он у вас включен:

Проверить статус nginx можно с помощью команды:
```
systemctl status nginx
```
Убедитесь с машины с Kali, что HTTP сервер на Ubuntu открывается на 80 порту (для этого в браузере откройте http://10.0.0.1 - браузер автоматически обратиться по порту 80):

Можете дополнительно удостовериться с помощью WireShark, что данные передаются в открытом виде.

### Настройка и генерация сертификатов
Каталог <code>/etc</code> - общий каталог, в котором хранятся ключевые конфигурационные файлы системы и сетевых сервисов. После установки Nginx, появится там и каталог для Nginx.

Далее сгенерируем самоподписанный сертификат с помощью OpenSSL:
```
sudo openssl req -newkey rsa:2048 -nodes -x509 -days 365 -keyout key.pem -out certificate.pem -subj "/C=RU/ST=Moscow/L=Moscow/O=Security/OU=Security/CN=netology.local" -addext "subjectAltName=DNS:netology.local"
```
Что это команда делает: генерирует сертификат и ключ с заполненными данными (в данном случае на домен netology.local).

В итоге в вашем каталоге должно появиться два файла:

1. <code>certificate.pem</code>
2. <code>key.pem</code>

Далее необходимо настроить конфигурационный файл nginx.
```
sudo vim /etc/nginx/sites-enabled/default
```
Вы увидите следующую картинку:
![HTTPcode.png](..%2F..%2Fimages%2FHTTPcode.png)
Вам нужно отредактировать имеющуюся конфигурацию, чтобы получить следующую:
```
server {
  # имя нашего сервера
  server_name netology.local;

  # слушаем на 443 порту
  listen 443 ssl;
  listen [::]:443 ssl;
  # пути к сертификату и ключу
  ssl_certificate /etc/nginx/certs/certificate.pem;
  ssl_certificate_key /etc/nginx/certs/key.pem;
  # какие протоколы поддерживаем
  ssl_protocols TLSv1.3;

  # где искать файлы, выдаваемые пользователю
  root /var/www/html;

  # какой файл выдавать по умолчанию
  index index.html index.htm index.nginx-debian.html;

  location / {
    try_files $uri $uri/ =404; 
  }     
}

server {
  server_name netology.local;

  if ($host = netology.local) {
    return 301 https://$host$request_uri;
  }

  listen 80;
  listen [::]:80;

  return 404;
}
```
Проверяем конфигурацию на отсутствие ошибок с помощью команды:
```
sudo nginx -t
```
Загружаем новую конфигурацию nginx:
```
sudo nginx -s reload
```
### Настройка Kali
Теперь мы хотим по домену netology.local обращаться к тому серверу, который работает по адресу 10.0.0.1. Самый простой способ для этого (чтобы не поднимать DNS) - это отредактировать файл <code>/etc/hosts</code>.

В Kali:
```
sudo vim /etc/hosts
```
Приведите его к следующему виду:
```
127.0.0.1    localhost
127.0.1.1    kali
10.0.0.1     netology.local
```
После этого в браузере откройте https://netology.local (и нажмите на кнопку <code>Advanced</code>):
![HTTPcode.v2.png](..%2F..%2Fimages%2FHTTPcode.v2.png)
Что мы видим? Фактически, нам говорят, что сертификат соответствует домену, но не является доверенным, поскольку является самоподписанным.

Если нажать на <code>View Certificate</code>:
![HTTPcode.v3.png](..%2F..%2Fimages%2FHTTPcode.v3.png)
Теперь проверим часть, которую мы настроили в нашем конфиге для перехода с http на https с кодом (301):
```
server {
  server_name netology.local;

  if ($host = netology.local) {
    return 301 https://$host$request_uri;
  }

  listen 80;
  listen [::]:80;

  return 404;
}
```
Введите адрес http://netology.local и удостоверьтесь, что вас перебрасывает на https://netology.local (можете посмотреть в консоли разработчика - F12):
![HTTPcode.v4.png](..%2F..%2Fimages%2FHTTPcode.v4.png)
Теперь, если вы нажмёте на <code>Accept Rist and Continue</code>, браузер вам загрузит данные по HTTPS, что вы можете увидеть в WireShark:
![HTTPcode.v5.png](..%2F..%2Fimages%2FHTTPcode.v5.png)
Посмотреть сертификат можно щёлкнув на замочке в адресной строке:
![HTTPcode.v6.png](..%2F..%2Fimages%2FHTTPcode.v6.png)
Нажмите <code>More Information</code>:
![HTTPcode.v7.png](..%2F..%2Fimages%2FHTTPcode.v7.png)
Далее <code>View Certificate</code>:
![HTTPcode.v8.png](..%2F..%2Fimages%2FHTTPcode.v8.png)
И даже его можно экспортировать вкладка <code>Details</code> и кнопка <code>Export</code>:
![HTTPcode.v9.png](..%2F..%2Fimages%2FHTTPcode.v9.png)

### HTTP2
Для включения HTTP2 достаточно в конфигурацию (на Ubuntu файл <code>/etc/nginx/sites-enabled/default</code>) включить <code>http2</code>:
```
server {
  server_name netology.local;

  listen 443 ssl http2;
  listen [::]:443 ssl http2;
  # дальше без изменений
```
Сохраняете файл, тестируете (<code>sudo nginx -t</code>), перезагружаете конфигурацию (<code>sudo nginx -s reload</code>).

В Kali удостоверяетесь, что вам теперь страничка отдаётся по HTTP2:
![HTTPcode.v10.png](..%2F..%2Fimages%2FHTTPcode.v10.png)
### Задача
Теперь о том, что вам нужно сделать: настройте сервер аналогично задаче, пришлите:

- сертификат
- пришлите скриншоты открытия веб-сервера в Windows (в браузере Chrome), на которых видно:
  - что страница загружается по HTTP2
  - скриншот серийного номера сертификата при просмотре в браузере