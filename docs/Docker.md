# Docker

# Содержание

* [Начало работы](#начало-работы)
> * [Установка](#установка)
> * [Настройка после установки](#настройка-после-установки)

* [Основные инструменты](#основные-инструменты)
> * [Клонирование образа](#клонирование-образа)
> * [Поиск образа](#просмотр-контейнеров)
> * [Просмотр контейнеров](#просмотр-контейнеров)
> * [Запуск контейнера](#запуск-контейнера)
> * [Повторный запуск контейнера](#повторный-запуск-контейнера)
> * [Присоединение к контейнеру](#присоединение-к-контейнеру)
> * [Остановка контейнера](#остановка-контейнера)
> * [Удаление контейнера или образа](#удаление-контейнера-или-образа)
> * [Запуск команд в контейнере](#запуск-команд-в-контейнере)
> * [Сохранение изменений в контейнере](#сохранение-изменений-в-контейнере)
> * [Отправка данных на Docker Hub](#отправка-данных-на-docker-hub)
> * [Dockerfile](#dockerfile)
> * [Сборка проекта](#сборка-проекта)
> * [Уменьшение размера образа](#уменьшение-размера-образа)
> * [Инструменты для Docker](#инструменты-для-docker)

<!-- > * [](#) -->

* [Docker Compose](#docker-compose)
> * [Команды](#команды)
> * [Docker Compose file](#docker-compose-file)


# Начало работы

## Установка
[Начальная установка Docker](https://docs.docker.com/engine/install/) полностью описана для любой операционной системы.


## Настройка после установки
Для Linux, чтобы каждый раз не нужно было писать sudo, нужно прописать:

    sudo groupadd docker
    sudo usermod -aG docker $USER

И дальше нужно перезагрузить компьютер или перелогиниться, чтобы изменения вступили в силу.

Если требуется чтобы Docker автоматически загружался при старте операционной системы нужно указать это в настройках (systemd).

    sudo systemctl enable docker.service
    sudo systemctl enable containerd.service

Для отключения этого.

    sudo systemctl disable docker.service
    sudo systemctl disable containerd.service

Для проверки что Docker установлен правильно.

    docker run hello-world
    docker ps -a

И после этого должен отображаться контейнер hello-world.

Дальше этот образ не требуется, поэтому его можно удалять.

    docker rm $(docker ps -aq)
    docker rmi hello-world


# Основные инструменты
## Клонирование образа
Команда для клонирования образа.

    docker pull <image-name>

По стандарту будет качаться latest версия, но через двоеточие, можно указать версию image.

    docker pull <image-name>:<version>

Склонированный образ является неизменяемым.


## Поиск образа
Для поиска образа используется команда `search`. Она будет выводить все найденные варианты с Docker Hub.

    docker search <image-name>

Официальный сайт где можно найти все контейнеры и описание к ним [Docker Hub](https://hub.docker.com/search?q=).


## Просмотр контейнеров
Для просмотра запущенных контейнеров.

    docker ps

Если указать флаг `-a`, то будут показаны все образы которые существуют.

    docker ps -a

Для просмотра размера каждого контейнера используется флаг `-s`.

    docker ps -s

Так же можно комбинировать флаги.

    docker ps -as

Для просмотра только id контейнеров используется флаг `-q`.

    docker ps -q

Для просмотра определенного контейнера, с полной его информацией.

    docker inspect <container-name>
    docker inspect <container-id>

Для просмотра логов контейнера.

    docker logs <container-name>
    docker logs <container-id>


## Запуск контейнера
Изначальный запуск контейнера. Если образ никогда не был установлен, то сначала Docker скачает образ, а потом запустит его.

    docker run <image-name>:<version>

Для работы внутри контейнера требуется указать флаги -it (-i interactive, -t - tty).

    docker run -it <image-name>:<version>

Для старта работы контейнера в фоновом режиме нужно использовать флаг -d (-d - detach).

    docker run -d <image-name>:<version>

При обычном запуске генерируется рандомное имя контейнеры. Для того, чтобы дать самостоятельно имя требуется использовать флаг `--name`.

    docker run -it --name <container-name> <image-name>:<version>

Для того чтобы контейнеры взаимодействовали с основной машиной, нужно установить им порты, по которым они будут взаимодействовать.
Для этого используется флаг `-p`, и после этого указывается порт вашей машины, и порт который будет в докер контейнере

    docker run -d --name <container-name> -p 8080:8080 <image-name>:<version>

Для того чтобы передать контейнеру файлы основной машины, можно воспользоваться флагом `-v`,
где требуется указать директорию, которая будет монтироваться, и потом куда она будет монтироваться.

    docker run -it --name <container-name> -v <path-to-host-directory>:<path-to-container-directory> <image-name>:<version>

## Повторный запуск контейнера
Если использовать `run`, то Docker будет создавать новый контейнер.
Но если требуется запустить контейнер, который уже когда-то использовался, используется команда `start`.
Запускать контейнер можно как и по имени контейнера, так и по его id.

    docker start -it <container-name>
    docker start -it <container-id>


## Присоединение к контейнеру
Команда, для присоединения к рабочему контейнеру.

    docker attach <container-name>
    docker attach <container-id>

Для выхода из него обычно используется `exit`.

## Остановка контейнера
Для остановки контейнера используется команда `stop`.

    docker stop <container-name>
    docker stop <container-id>

Для остановки всех контейнеров.

    docker stop $(docker ps -aq)

Принудительное завершение контейнера.

    docker kill <container-name>
    docker kill <container-id>


## Удаление контейнера или образа
Для удаления контейнера нужно использовать rm, удалять можно как и по имени контейнера, так и по его id.

    docker rm <container-id>
    docker rm <container-name>

Если требуется удалить контейнеры по имени образа, то можно воспользоваться командой.

    docker rm $(docker ps -a | awk '$2=="<image-name>" { print $1 }')

Для удаления образа нужно использовать rmi, при этом все контейнеры основанные на нём должны быть удалены.

    docker rmi <image-name>


## Запуск команд в контейнере
Для запуска команд используется команда `exec`.

    docker exeс -it <container-name> command
    docker exeс -it <container-id> command


## Сохранение изменений в контейнере
Для сохранения изменений требуется его закоммитить

    docker commit <container-name> <username>/<container-name>:<vesion>
    docker commit <container-id> <username>/<container-name>:<vesion>


## Отправка данных на Docker Hub
Для отправки данных требуется авторизация

    docker login

И после этой команды, консоль запрашивает логин и пароль для авторизации на сайте [docker](https://hub.docker.com/)

И после этого можно передать данные на Docker Hub.

    docker push


## Dockerfile
Dockerfile является настройкой собственного контейнера. Каждая новая инструкция будет создавать новый слой контейнера.

Унаследование от другого image, образ с которого будет происходить старт.

    FROM <image-name>:<version>

    # example
    FROM ubuntu:20.04

Метаданные для образа.

    LABEL <key>=<value> [<key>=<value> ...]

    # example
    LABEL maintainer="user@example.org" version="1.1.1"

Регистрация переменной окружения.

    ENV <key> <value>

    # example
    ENV LEVEL debug

Указания рабочей директории, в какой директории будет находиться контейнер, от куда будет запуск команд.
Если директория не создана, тогда docker сам создаст эту директорию.

    WORKDIR <path>

    # example
    WORKDIR /home/user

Копирования файлов из контекста в образ.
Копирование файлов будет происходить из контекста где находится Dockerfile и ниже, выше подниматься нельзя.

    COPY <src> [<src> ...] <dst>
    # <src> - файл или директория внутри build контекста
    # <dst> - файл или директория внутри контейнеры

    # example
    COPY . /home/user/

Добавление файлов работают ровно так же как и COPY, но так же можно добавлять вместо локальных файлов файлы из интернета.

    ADD <src> [<src> ...] <dst>
    # <src> - файл или директория внутри build контекста
    # <dst> - файл или директория внутри контейнеры

    # добавление по url
    ADD url /home/user/

Вызов команд. Для уменьшения слоев можно писать несколько команд в одном запуске.

    RUN <command>

    # example
    RUN apt-get update && apt-get install nginx

Указание какая команда будет выполняться при старте контейнера.

    CMD <command>

    # example
    CMD /start.sh

Аналог CMD - ENTRYPOINT.

    ENTRYPOINT <command>

    # example
    ENTRYPOINT /start.sh

Указание точки монтирования томов внутри образа.

    VOLUME <dst> [<dst> ...]
    # <dst> - директория монтирования для volume'a

    # example
    VOLUME /app /db /data

Указание портов, которые слушает сервис в запущенном контейнере.

    EXPOSE <port>[/<proto>] [<port>[/<proto>] ...]
    # <port> - порт сервиса внутри контейнера
    # <proto> - tcp или udp

    # example
    EXPOSE 8080/tcp 3389/udp

Команда, которая запускается при сборке образа, на базе текущего.

    ONBUILD <command>

Указывается сигнал, который посылается процессу при остановке контейнера.

    STOPSIGNAL <sig>

Имя (ID) пользователя, от которого выполняются директивы RUN, CMD, ENTRYPOINT.

    USER <username>

Почти как ENV, но задаёт параметры только для docker build.
Если в ENV мы задавали константные переменные, то здесь они будут задаваться динамически.

    ARG <string>


Команда, которой можно проверить состояние сервиса.

    HEALTHCHECK <command>


[Документация по всем командам](https://docs.docker.com/engine/reference/builder/).


## Сборка проекта
Сборка всего проекта в один контейнер.

Опция `-t` или `--tag` указывает тег для контейнера.

Опция `-f` или `--file` указывает путь до Dockerfile.

Опция `--squash` указывает что все слои будут соединены в один.

Точка в конце указывает текущую директорию как место сборки. Так же можно указать путь до директории.

    docker build -t <username>/<container-name>:<vesion> .

Полная документация по [docker build](https://docs.docker.com/engine/reference/commandline/build/).


## Логи контейнера
Для просмотра логов, которые отдаёт контейнер есть атрибут `logs`.

    docker logs <container-name>
    docker logs <container-hash>


## Уменьшение размера образа
1) Избегать установки лишних пакетов и упаковки лишних данных в образы.
2) Использовать связанные команды для RUN инструкций.

```
RUN command_1 && command_2 && command_3
```

Или можно привести к более читаемому виду

    RUN command_1 && \
        command_2 && \
        command_3

3) Следить за последовательностью описания Dockerfile, избегать cache miss.
4) Уменьшать количество слоёв.
5) Один контейнер - одна задача.
6) Чистить за собой в контейнере.
7) Использовать multi-stage сборки (для компилируемых языков).
8) Использовать scratch образ для собственных приложений. Scratch - зарезервированный образ Docker, в котором ничего нет (пустой Dockerfile).


## Инструменты для Docker
Для удобного просмотра контейнеров, какие запущены, какие простаивают, сколько памяти тратят и пр., есть утилита `ctop`

Документацию и установку можно найти на [github](https://github.com/bcicen/ctop)


# Docker Compose
Docker Compose используется для одновременного управления несколькими контейнерами, входящими в состав приложения.
Этот инструмент предлагает те же возможности, что и Docker, но позволяет работать с более сложными приложениями.

Описывается всё в файле docker-compose.yml.
Это файл, который будет содержать инструкции, необходимые для запуска и настройки сервисов.
Обычно он хранится в корневой директории проекта.


## Команды
Сборка проекта.

    docker-compose build

Запуск проекта. Ключ `-d` означает, что запуск будет в фоновом режиме.

    docker-compose up -d

Остановка проекта.

    docker-compose down

Посмотр логов сервиса.

    docker-compose logs -f [service-name]

Вывод списка контейнеров.

    docker-compose ps

Выполнение команды в контейнере.

    docker-compose exec [service-name] [command]

Показать список образов.

    docker-compose images

Справка по всем командам.

    docker-compose --help


## Docker Compose file
Полная документация по [docker compose file](https://docs.docker.com/compose/compose-file).

[Документация по 3, последней версии.](https://docs.docker.com/compose/compose-file/compose-file-v3/)

В начале файла указывается версия compose.
В различных версиях добавлены/удалены/изменены службы.
Все различные [версии и изменения](https://docs.docker.com/compose/compose-file/compose-versioning/).

    version: "3.9"

При указании используемой версии файла Compose, при версиях 2 и 3, нужно указать как старший, так и дополнительный номера.
Если дополнительная версия не указана, по умолчанию используется 0, а не последняя дополнительная версия.

    version: "3"
    # is equivalent to
    version: "3.0"

Дальше идёт перечисление сервисов. И в этих сервисах описываются служб.

    services:
      name_1:
      name_2:
      name_3:

`build` - указание пути, где лежит Dockerfile для определенного проекта.

    # example
    # в данном случае будет искаться файл Dockerfile
    build: ./directory

    # example
    # в данном случае указан определённый Dockerfile
    build: ./directory/Dockerfile.new


`image` - указание определённого image.

    image: image:version

`ports` - Открытие контейнерных портов.

    ports:
      - <host-port>:<container-port>

    # example
    ports:
      - 8080:80
      - "6060:6060/tcp"


`expose` - определяет порты, которые должны предоставлять из контейнера.

    expose:
      - <port>

    # example
    expose:
      - "3000"
      - "8000"

`env_file` - Добавление переменные среды из файла. Может быть одним значением или списком.

    env_file: <env-file>

    env_file:
      - <env-file-1>
      - <env-file-2>
      - <env-file-3>

env_file:

    # - комментарий
    VARIABLE=VALUE

    # example
    MY_VAR=value

`environment` - Добавление переменные среды. Можно использовать либо массив, либо словарь.

    environment:
      SHOW: 'true'
      PASSWORD: "PASSWORD"

    environment:
      - SHOW=true
      - PASSWORD="password"

`volumes` - Указание точки монтирования томов внутри образа.

    volumes:
      # Абсолютный путь.
      - /opt/data:/var/lib/mysql

      # Путь на хосте, относительно Compose файла.
      - ./cache:/tmp/cache

      # Пользовательский путь.
      - ~/configs:/etc/configs/:ro

`container_name` - Собственное имя контейнера, вместо генерации по умолчанию.

    container_name: <container-name>

`entrypoint` - Переопределение начального запуска.

    entrypoint: <command>

    # Так же можно задать её списком
    entrypoint: ["<command>", "<arg1>", "<arg2>"]

`depends_on` - зависимости запуска и завершения работы между службами.

    # example
    services:
      name_1:
        depends_on:
          - name_2
          - name_3
      name_2:
      name_3:

    # служба name_1 зависит от name_2 и name_3
    # т.е. будет запускаться после них
