## NextCloud

### В контейнерах docker
2. Создадим директорию /etc/nextcloud
```
mkdir /etc/nextcloud
```
3. Назначим на нее полные права всем пользователям на редактирование:
```
chmod 777 -R /etc/nextcloud
```
4. Созадим файл docker-compose.yaml в /etc/nextcloud:
```
version: '2'

volumes:
  nextcloud:
  db:

services:
  db:
    image: mariadb:10.6
    restart: always
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=dbpass
      - MYSQL_PASSWORD=dbpass
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud:fpm
    restart: always
    links:
      - db
    volumes:
      - nextcloud:/var/www/html
    environment:
      - MYSQL_PASSWORD=dbpass
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db

  web:
    image: nginx
    restart: always
    ports:
      - 8080:80
    links:
      - app
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    volumes_from:
      - app
```
5. Запустим наш контейнер, находясь в директории /etc/nextcloud:
```
docker-compose up -d 
```
6. Теперь наш веб-сервер должен быть доступен по адресу http://<ip-адрес>:8080
