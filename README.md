## Nextcloud + Nginx + OnlyOffice + Let´s Encrypt + Proxy + Redis

В данной сборке Docker есть все необходимое для быстрого запуска Nextcloud версии alpine и настройки SSL с помощью Let´s Encrypt. В качестве примера приведена ubuntu server 20.04.

## Требования

* Последняя версия Docker
* Последняя версия Docker Compose

## Перед началом

Если необходимо остановить и очистить все старые контейнера:

    docker kill $(docker ps -q)
        
Удалить все контейнера и кэш

    docker system prune --volumes -a
        
## Установка

1. Устанавливаем и настраиваем Docker и Docker Compose:
    Обновляем все пакеты:
    
        sudo apt update
    Затем устанавливаем несколько необходимых пакетов, которые позволяют использовать пакеты через HTTPS:
        
        sudo apt install apt-transport-https ca-certificates curl software-properties-common
    Добавляем ключ GPG для официального репозитория Docker в нашу систему:
    
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    Добавляем репозиторий docker в источники APT:
    
        sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
    Обновляем базу данных пакетов и добавляем в нее пакеты Docker из недавно добавленного репозитория:
    
        sudo apt update
    Устанавливаем Docker:
    
        sudo apt install docker-ce
    Проверяем статус процесса:
    
        sudo systemctl status docker
    Проверяем текущую версию и обновляем ее при необходимости:
    
        sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    Настроим разрешения:
        
        sudo chmod +x /usr/local/bin/docker-compose
    Проверим версию Docker Compose
        
        docker-compose --version
    Docker и Docker Compose настроены.

2. Начинаем работу с образом
    Клонируем репозиторий:
    
        git clone https://github.com/fakefedya/docker-nextcloud-onlyoffice.git
    Переходим в директорию:
   
        cd docker-nextcloud-onlyoffice/config
    Конфигурируем файлы настроек:
    - mariadb
    - nextcloud
    - nginx
   
   Загружаем и собираем проект:

        docker-compose build --pull
   Запускаем проект:

        docker-compose up -d
    ** Внимание **: Процесс может занять продолжительное время.
    
   Если при запуске появляется ошибка: _"Network nginx-proxy declared as external"_, выполняем команду:

        docker network create nginx-proxy
   Проверяем наличие сети nginx-proxy:
        
        docker network ls     
   Если сеть есть, повторно запускаем проект:
    
        docker-compose up -d     
   Запускаем браузер и переходим на стартовую страницу nextcloud, авторизуемся.
   
   Возвращаемся в директорию проекта и запускаем `set_configuration.sh` скрипт настройки:
   
   ** Внимание **: Скрипт должен быть запущен с правами root.

        bash set_config.sh 
   На данном этапе сервер запущен, приложение работает, SSL настроено.

## Возможные ошибки

1. Если при открытии страницы Nextcloud видим ошибку _"Доступ через недоверенный домен"_, конфигурируем файл:

        nano nextcloud/config/config.php  
    Добавляем IP адрес / доменное имя в секцию _"trusted_domains"_, после чего перезапускаем контейнер с Nextcloud.

2. Если при перезапуске Docker на странице Nextcloud появляется ошибка, связанная с redis, добавляем параметр перезапуска контейнера redis в docker-compose.yml:
      
        restart: alwaws

## Решение проблем на странице состояния Nextcloud

1. Если сервер ругается на некорретнонастроенный прокси:

    Конфигурируем файл настроек Nextcloud:
    
        nano nextcloud/config/config.php
  `'trusted_domains' => 
  array (
    0 => 'yourdomain.com',
  ),
  'trusted_proxies' => 
  array (
    0 => 'yourdomain.com',
  ),
  'overwrite.cli.url' => 'https://yourdomain.com',
  'overwriteprotocol' => 'https',`
    Перезапускаем контейнер Nextcloud.

2. Уведомление о HTTP «X-Frame-Options»

    Конфигурируем файл настроек Nginx:
    
        sudo nano /config/nginx/nginx.conf
    Добавляем `add_header X-Frame-Options "SAMEORIGIN";` в секцию "server".
    
