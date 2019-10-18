# st1ngrayone_microservices
st1ngrayone microservices repository

## HW # 18 Logging-1 

- Создан отдельный compose файл для логирования `docker-compose-logging.yml`
```
version: '3'
services:
fluentd:
build: ./fluentd
ports:
- "24224:24224"
- "24224:24224/udp"
elasticsearch:
image: elasticsearch
expose:
- 9200
ports:
- "9200:9200"
kibana:
image: kibana
ports:
- "5601:5601"
```
- Собран контейнер fluentd с конфигурацией для нашей системы
`cd logging/fluentd/ && docker build -t $USERNAME/fluentd .`
- В docker-compose.yml сервиса post добавлена конфигурация для сбора логов при помощи fluentd
```
logging:
driver: "fluentd"
options:
fluentd-address: localhost:24224
tag: service.post
```
- Протестировали визуализацию при помощи kibana
- Добавлены регулярные выражения для сбора неструктурированных логов в docker/fluent/fluentd.conf
- Настроены grok шаблоны
- Добавлен zipkin в файл docker-compose-logging.yml

### Задание со * zipkin
- Развернут код из bugged репозитория. 
- При помощи zipkin трейса удалось выяснить, что сервис comment отдает ошибку 500 при запросе /5da85228270cf6000b0c12be/comments
- Как исправить, к сожалению не догадался:( 

## HW # 17 Monitoring-2

- Создан отдельный compose файл с инфраструктурой для мониторинга контейнеров
- Добавлен контейнер grafana настроен сбор метрик через cadvisor
- Подключены grafana дашборды "Docker and system monitoring"
- Настроен дашборд с функцией rate для визуализации запросов с кодами ошибки 4хх или 5хх
- Настроен дашборд с функцией rate для визуализации успешных http запросов
`rate(ui_request_count{http_status=~"^[23].*"}[1m])`
- Добавлены дашборды для мониторинга бизнес логики grafana/dashboards
- Добавлен alertmanager в compose файл, пересобраны образы
- Настроены алертинг в слак канал #anton_cherenkov
- Запушены созданные образы на dockerhub https://cloud.docker.com/u/st1ngrayone/repository/list 
 
## HW # 16 Monitoring-1

- Создан докер-хост в GCP 
- Запущен и протестирован образ прометея. 
- Добавлена сборка образов скриптом в docker-build.sh
- Создан docker-compose файл с учетом prometeus и reddit сервисов протестирована отдача метрик
- Образы загружены на docker-hub https://cloud.docker.com/u/st1ngrayone/repository/list
```
$ docker login
Login Succeeded
$ docker push $USER_NAME/ui
$ docker push $USER_NAME/comment
$ docker push $USER_NAME/post
$ docker push $USER_NAME/prometheus 
```
- Добавлен job node_exporter в прометей

## HW # 15 Gitlab-ci

- Создан docker-host при помощи docker-machine 
- Установлен Gitlab через docker-compose файл 
`docker-compose up -d`
- Настроен runner для работы с gitlab ci 
- Настроен проект в gitlab и настроен ci
- Настроены среды, динамические окружения, условия запуска тестов в пайплайне

## HW # 14 Docker network, docker-compose

- Запущен контейнер с использованием none и host драйвера
- Настроены 2 сети в соответствии со схемой
`docker network create front_net back_net`
- Запущены контейнеры с подключенными bridge сетями front_net и back_net
- Подключены недостающие сети к контейнерам 
```
docker nework connect front_net post
docker nework connect front_net comment
```
- Установлен docker-compose
`pip3 install docker-compose`
- Создан docker-compose.yml файл, параметризированы значения 
- Заданы переменные в .env файле
- Базовое имя проекта можно установить при запуске docker-compose с помощью параметра -p
`docker-compose -p dockermicroservices up -d`


## HW # 13

- Собраны образы для каждого из контейнеров приложения
- Приложение разделено на несколько компонентов
- Создана docker-сеть reddit
`docker network create reddit`
- Создан volume и подключен к контейнеру с post_db
`docker volume create reddit_db`
- Оптимизирован размер образа ui путем изменения содержимого dockerfile

* Команда запуска контейнеров:
```
docker run -d --network=reddit --network-alias=post_db \
--network-alias=comment_db -v reddit_db:/data/db mongo:latest
$ docker run -d --network=reddit --network-alias=post st1ngrayone/post:1.0
$ docker run -d --network=reddit --network-alias=comment st1ngrayone/comment:1.0
$ docker run -d --network=reddit -p 9292:9292 st1ngrayone/ui:1.0
```

## HW # 12

- Создан docker-host в GCP
- Создан образ reddit и залит в docker-hub

