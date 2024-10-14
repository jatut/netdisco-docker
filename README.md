[![CPAN version](https://badge.fury.io/pl/App-Netdisco.svg)](https://metacpan.org/pod/App::Netdisco)
[![Build Status](https://travis-ci.org/netdisco/netdisco.svg?branch=master)](https://travis-ci.org/netdisco/netdisco)
[![Docker Image](https://img.shields.io/badge/docker%20images-ready-blue.svg)](https://store.docker.com/community/images/netdisco/netdisco)

**Netdisco** - это веб-инструмент управления сетью, подходящий как для небольших, так и для очень больших сетей. Данные об IP- и MAC-адресах собираются в базу данных PostgreSQL с помощью SNMP, CLI или API-интерфейсов устройств. Некоторые вещи, которые вы можете делать с Netdisco:

* Найти машину в сети по MAC или IP и укажите порт коммутатора, на котором она находится.
* Выключить порт коммутатора или измените статус VLAN или PoE порта.
* Провести инвентаризацию сетевого оборудования по модели, поставщику, программному обеспечению и операционной системе.
* Красивые фотографии вашей сети

Посмотрите демо-версию по адресу: [https://netdisco2-demo.herokuapp.com/](https://netdisco2-demo.herokuapp.com/)

**Netdisco** включает в себя легкий веб-сервер для интерфейса, серверный демон для сбора данных из вашей сети и интерфейс командной строки для устранения неполадок. Существует простой файл конфигурации в формате YAML.

##  Docker Deployment

Контейнерам нужны некоторые каталоги, присутствующие в смонтированном томе. В каталоге по вашему выбору создайте эту структуру и разрешите uid netdisco в контейнере (901) записывать в нее:

    cd $directory_of_your_choice
    mkdir -p netdisco/{logs,config,nd-site-local} 
    sudo chown -R 901:901 netdisco

*(этот шаг необходим на хостах Linux и может быть пропущен в версиях Docker для OS X и Windows)*

Загрузите docker-compose.yml в нужный и запустите его.

    curl -Ls -o docker-compose.yml https://tinyurl.com/nd2-dockercompose
    docker-compose up

Это запустит базу данных, внутренний демон и веб-интерфейс, прослушивающий порт 5000. Если у вас есть устройство, использующее общедоступное сообщество SNMP, отличное от `public`, введите его на домашней странице Netdisco и нажмите  "Discover"(Обнаружить).

The default configuration is available in `netdisco/config/deployment.yml`. The backend and web daemons will automatically restart when you save changes to this file. Logs are available in `netdisco/logs/`.

Конфигурация по умолчанию доступна в `netdisco/config/deployment.yml`. Серверная часть и веб-демоны автоматически перезапустятся, когда вы сохраните изменения в этом файле. Журналы доступны в `netdisco/logs/`.

Утилита [netdisco-do](https://metacpan.org/dist/App-Netdisco/view/bin/netdisco-do) может быть запущена следующим образом (запустите ее без `<action>`, чтобы получить помощь):

    docker-compose run netdisco-do <action>

Локальные веб-плагины или внутренние плагины можно установить в `netdisco/nd-site-local/` согласно [нашей документации](https://github.com/netdisco/netdisco/wiki). Наконец, файлы данных PostgreSQL хранятся в `netdisco/pgdata/`, и мы не советуем их трогать (если только вы не хотите повторно инициализировать систему).

Веб-интерфейс изначально настроен на разрешение доступа без аутентификации с полными правами администратора. Мы предлагаем вам посетить пункт меню `Admin -> User Management(«Администратор» -> «Управление пользователями»)` и установить `no_auth: false` в файле `deployment.yml`, чтобы удалить эту гостевую учетную запись и настроить доступ для аутентифицированных пользователей.

Other username, password, database connection, and file locations, can all be set using [environment variables](https://github.com/netdisco/netdisco/wiki/Environment-Variables) described in our wiki. Of course the database container is optional and you can connect to an existing or external PostgreSQL server instead.

Другое имя пользователя, пароль, подключение к базе данных и расположение файлов можно установить с помощью переменных среды, [описанных в нашей вики](https://github.com/netdisco/netdisco/wiki/Environment-Variables). Конечно, контейнер базы данных не является обязательным, и вместо этого вы можете подключиться к существующему или внешнему серверу PostgreSQL.

##  Рекомендованная версия Docker и docker-compos

 * Docker 20.10.0 (Linux) or Docker Desktop 3.3.0 (Win/Mac) 
 * docker-compose 1.28

## Получение поддержки

У нас есть несколько других страниц с советами по [настройке и устранению неполадок Netdisco](https://github.com/netdisco/netdisco/wiki/Troubleshooting), [советами и рекомендациями для конкретных платформ](https://github.com/netdisco/netdisco/wiki/Vendor-Tips), а [также всеми вариантами конфигурации](https://github.com/netdisco/netdisco/wiki/Configuration).

Вы также можете поговорить с кем-нибудь на IRC-канале [`#netdisco@libera`](https://kiwiirc.com/nextclient/irc.libera.chat/netdisco) или в [списке рассылки электронной почты](https://lists.sourceforge.net/lists/listinfo/netdisco-users) сообщества.

## Обновление

Загрузите новый образов и пересоздайте контейнер с помощью команд ёdocker-compose down; docker-compose pull ; docker-compose up --force-recreateё — это все, что нужно. Когда наш образ базы данных запускается, он всегда обновляет схему БД до последней версии. Чтобы обновить собственную базу данных PostgreSQL, выполните:

    docker-compose run --entrypoint=bin/netdisco-db-deploy netdisco-backend

## Благодарности

Спасибо Айре Снайдеру и ЛБеньо за вдохновение. Спасибо также проекту PostgreSQL за отличные примеры магии докера. Мы создаем при поддержке отличного сервиса GitHub Actions.

