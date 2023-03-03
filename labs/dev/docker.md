# Контейнеризация (Docker)
## Задание №1. Создание Docker Image
Создайте контейнер с использованием Scratch и запустите его

В качестве результата работы отправьте:

* Скриншот вывода команды docker image ls.
![image_ls.png](images%2Fimage_ls.png)
* Скриншот вывода команды docker container run ibdevapp.
![container_run.png](images%2Fcontainer_run.png)

---

## Задание №2. Multistage Build
В результате этой лабораторной работы вы:

1. Создадите образ на базе образа, в котором установлен Go;
2. Соберёте ваше приложение;
3. Используете результаты сборки для построения финального образа.

В качестве результата работы отправьте:

* Скриншот вывода команды docker image ls.
![ls_2.png](images%2Fls_2.png)
* Скриншот вывода команды docker-compose up --build.
![build.png](images%2Fbuild.png)