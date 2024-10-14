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

This will start the database, backend daemon, and web frontend listening on port 5000. If you have a device using the SNMP community `public`, enter it in the Netdisco homepage and click "Discover".

The default configuration is available in `netdisco/config/deployment.yml`. The backend and web daemons will automatically restart when you save changes to this file. Logs are available in `netdisco/logs/`.

The [netdisco-do](https://metacpan.org/dist/App-Netdisco/view/bin/netdisco-do) utility can be run like this (run it without `<action>` to get help):

    docker-compose run netdisco-do <action>

Local web or backend plugins can be installed into `netdisco/nd-site-local/` as per [our documentation](https://github.com/netdisco/netdisco/wiki). Finally, the PostgreSQL data files are stored in `netdisco/pgdata/` and we do not advise touching them (unless you wish to reinitialize the system).

The web frontend is initally configured to allow unauthenticated access with full admin rights. We suggest you visit the `Admin -> User Management` menu item, and set `no_auth: false` in `deployment.yml`, to remove this guest account and set up authenticated user access.

Other username, password, database connection, and file locations, can all be set using [environment variables](https://github.com/netdisco/netdisco/wiki/Environment-Variables) described in our wiki. Of course the database container is optional and you can connect to an existing or external PostgreSQL server instead.

##  Docker Requirements

 * Docker 20.10.0 (Linux) or Docker Desktop 3.3.0 (Win/Mac) 
 * docker-compose 1.28

## Getting Support

We have several other pages with tips for [understanding and troubleshooting Netdisco](https://github.com/netdisco/netdisco/wiki/Troubleshooting), [tips and tricks for specific platforms](https://github.com/netdisco/netdisco/wiki/Vendor-Tips), and [all the configuration options](https://github.com/netdisco/netdisco/wiki/Configuration).

You can also speak to someone in the [`#netdisco@libera`](https://kiwiirc.com/nextclient/irc.libera.chat/netdisco) IRC channel, or on the [community email list](https://lists.sourceforge.net/lists/listinfo/netdisco-users).

## Upgrading

Pulling new images and recreating the containers with `docker-compose down ; docker-compose pull ; docker-compose up --force-recreate` is all there is to it. When our database image starts it always updates the DB schema to the latest release. To upgrade your own PostgreSQL database, run:

    docker-compose run --entrypoint=bin/netdisco-db-deploy netdisco-backend

## Credits

Thanks to Ira W. Snyder and LBegnaud for inspiration. Thanks also to the PostgreSQL project for great examples of docker magic. We build with the support of the excellent GitHub Actions service. 
