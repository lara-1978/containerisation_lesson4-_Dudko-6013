# Dockerfiles: слои и сборка образов

## Задача

Дома необходимо собрать образ и запустить из него контейнер:

Основой образа должна быть alpine.
Установить необходимо mariaDB
Также не забудьте об уменьшении размера образа. Способ обсуждался на лекции.
Необходимо открыть порт для коммуникации с другими сущностями.
Для проверки решения необходимо подключить к такому контейнеру phpmyadmin. Необходимо, чтобы в нем вы увидели 
данные из вашей БД.
Также при запуске необходимо смонтировать внешнюю папку для хранения данных БД вне контейнера.

# Решение

### 1 Создаем Dockerfile

FROM alpine
RUN apk update && apk add mariadb mariadb-client && apk add bash
RUN mysql_install_db --user=mysql
ENV TERM = xterm
COPY mariadb_conf/* /etc/mysql/
VOLUME /var/lib/mysql
VOLUME /var/log/mysql
RUN mkdir -p /var/backups/mysql
RUN chmod a+r /var/backups/mysql/
VOLUME /var/backups/mysql
ENTRYPOINT ["mysql_test"]
EXPOSE 3306
                                                                                                    --01.png
### 2 сборка

docker build -t my_mariadb .                                                                        --2.png

создался контейнер                                                                                  --4.png

### 3 Устанавливаем mariadb

 docker run --detach --name some-mariadb --env MARIADB_ROOT_PASSWORD=my-secret-pw  mariadb:latest   --3.png

 #### 4 проверяем работает ли контейнер

 docker exec -it 316cdc24e22a mariadb -u root -p                                                    --5.png



 ### 4 подключились к  контейнеру phpmyadmin, увидели  нашу БД new_db

docker run --name phpmyadmin -d --link some-mariadb:db -p 8081:80 phpmyadmin                        --6.png