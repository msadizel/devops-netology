
# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"

## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Любые вопросы по решению задач задавайте в чате Slack.

---

## Задача 1

Сценарий выполения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

### Сборка образа
```
docker build -t msadizel/nginx:1.0.0 .
```

### Загрузка образа в DockerHub 
```
docker push msadizel/nginx:1.0.0 
```

### Запуск собственного nginx контейнера 
```
docker run -p 8080:80 --name msadizelnginx -d msadizel/nginx:1.0.0
```

[DockerHub](https://hub.docker.com/repository/docker/msadizel/nginx)

## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
- Nodejs веб-приложение;
- Мобильное приложение c версиями для Android и iOS;
- Шина данных на базе Apache Kafka;
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
- Мониторинг-стек на базе Prometheus и Grafana;
- MongoDB, как основное хранилище данных для java-приложения;
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

### Ответ 


- Высоконагруженное монолитное java веб-приложение. В данном случае целесообразнее использовать docker контейнер, для того чтобы ускорить развертываение.

- Nodejs веб-приложение. Простая сборка в docker контейнерах.

- Мобильное приложение c версиями для Android и iOS. Сборка Android может быть возможна в контейнере, а вот iOS собрать без apple'овского устройства, на сколько мне известно нельзя.

- Шина данных на базе Apache Kafka. Docker подходит для разворачивания этого сервиса.

- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana. Docker подходит для разворачивания этих сервисов.

- Мониторинг-стек на базе Prometheus и Grafana. Docker подходит для разворачивания этого сервиса.

- MongoDB, как основное хранилище данных для java-приложения. Можно взять публичный образ с DockeHub, подключить volume для хранения данных.

- Gitlab сервер для реализации CI/CD процессов и приватный Docker Registry. Можно использовать Docker, но на мой взгляд лучше виртуальная машина.


## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```;
- Добавьте еще один файл в папку ```/data``` на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

### Запуск контейнера ***centos***
```
docker run -v /data:/data -it centos
```

### Запуск контейнера ***debian***
```
docker run -v /data:/data -it debian
```

### Добавление файла в ***centos***
```
vagrant@server1:~$ docker run -v /data:/data -it centos
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
a1d0c7532777: Pull complete
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
[root@ccef50247dd1 /]# cd data
[root@ccef50247dd1 data]# echo "Hi Netolog" > netology.txt
[root@ccef50247dd1 data]# ls -l
total 4
-rw-r--r-- 1 root root 11 Nov 21 14:13 netology.txt
[root@ccef50247dd1 data]#
```

### Добавление файла и показ списка файлов в ***debian***
```
vagrant@server1:~$ docker run -v /data:/data -it debian
Unable to find image 'debian:latest' locally
latest: Pulling from library/debian
a8ca11554fce: Pull complete
Digest: sha256:3066ef83131c678999ce82e8473e8d017345a30f5573ad3e44f62e5c9c46442b
Status: Downloaded newer image for debian:latest
root@d564258a107c:/# cd
root@d564258a107c:~# ls
root@d564258a107c:~# cd /
root@d564258a107c:/# ls
bin  boot  data  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@d564258a107c:/# cd data/
root@d564258a107c:/data# ls
netology.txt
root@d564258a107c:/data# touch debian.txt
root@d564258a107c:/data# ls -l
total 4
-rw-r--r-- 1 root root  0 Nov 21 14:15 debian.txt
-rw-r--r-- 1 root root 11 Nov 21 14:13 netology.txt
root@d564258a107c:/data#
```

## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---