## Nextcloud + OnlyOffice + Let´s Encrypt + Nginx

В данной сборке докера есть все необходимое для быстрого запуска Nextcloud версии alpine и настройки SSL с помощью Let´s Encrypt

## Требования

* Последняя версия docker
* Последняя версия docker-compose

## Перед началом

Если необходимо остановить и очистить все контейнера:

    docker kill $(docker ps -q)
        
Удалить все контейнера и кэш

    docker system prune --volumes -a
        
## Установка

1. Клонировать репозиторий:

        git clone https://github.com/fakefedya/docker-nextcloud-onlyoffice.git
    
   Перейти в директорию:
   
        cd docker-nextcloud-onlyoffice/config
    
    
2. Задать настройки в конфигурационных файлах
    — mariadb
    — nextcloud
    — nginx

3. Загружаем и собираем проект:

        docker-compose build --pull

4. Запускаем проект:

        docker-compose up -d

    ** Внимание **: Процесс может занять продолжительное время.

5. Если при запуске появляется ошибка: _"Network nginx-proxy declared as external"_, выполняем команду:

        docker network create nginx-proxy
    
    Проверяем наличие сети nginx-proxy:
        
        docker network ls
        
    Если сеть есть, повторно запускаем проект:
    
        docker-compose up -d
        
6. Запускаем браузер и переходим на стартовую страницу nextcloud, выолняем первичные настройки

7. Возвращаемся в директорию проекта и запускаем `set_configuration.sh` скрипт настройки:

    ** Внимание **: Скрипт должен быть запущен с правами root.

        bash set_config.sh
    
8. Радуемся, все готово.

## Возможные ошибки

1. Если при открытии страницы Nextcloud видим ошибку _"Доступ через недоверенный домен"_, конфигурируем файл:

        nano nextcloud/config/config.php
      
    Добавляем локальный IP адрес в секцию _"trusted_domains"_, после чего перезапускаем контейнер с Nextcloud

2. Если при перезапуске docker на странице Nextcloud появляется ошибка, связанная с redis, добавляем параметр перезапуска контейнера redis в docker-compose.yml:
      
        restart: alwaws
