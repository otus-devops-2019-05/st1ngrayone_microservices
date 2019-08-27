# st1ngrayone_microservices
st1ngrayone microservices repository

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

