## Установка и настройка сайта с помощью NGINX


### Установка и настройка NGINX на серверах APP
1. Установка пакета
```
apt update -y && apt upgrade -y
apt install nginx -y
```
2. Удалим дефолтный сайт
```
rm /etc/nginx/sites-enabled/default
```
3. Создадим новый файл с конфигурацией нашего сайта /etc/nginx/sites-enabled/msk_site
```
server {
        listen 80 ;
        root  /var/www/ ;
        index index.html ;
        server_name web.jun39.msk ;
        location /     {
            try_files $uri $uri/ =404;
        }

}
```
4. Создадим файл index.html с содержимым нашего сайта в корневой директории
```
Welcome to Minecraft server mc.jun39.wsr site in XX region
```
5. Добавим наш сервис в автозагрузку и включим его
```
systemctl enable nginx --now
```

### Установка и настройка NGINX на сервере SRV1 c SSL HTTPS
1. Установка пакета
```
apt update -y && apt upgrade -y
apt install nginx -y
```
2. Удалим дефолтный сайт
```
rm /etc/nginx/sites-enabled/default
```
3. Создадим новый файл с конфигурацией нашего сайта /etc/nginx/sites-enabled/msk_site
```
server {
        listen 80 ;
        root  /var/www/ ;
        index index.html ;
        server_name corp.msk.jun39.wsr ;
        location /     {
            try_files $uri $uri/ =404;
        }
        return 301 https://$host$request_uri ;

}
server {
        listen 443 ;
        root  /var/www/ ;
        index index.html ;
        server_name corp.msk.jun39.wsr ;

        ssl_certificate /etc/nginx/conf.d/server.crt ;
        ssl_certificate_key /etc/nginx/conf.d/server.key;

        location /     {
            try_files $uri $uri/ =404;
        }
}
```
4. Создадим файл index.html с содержимым нашего сайта в корневой директории
```
Welcome to secure corporate portal jun39.wsr
```
5. Добавим наш сервис в автозагрузку и включим его
```
systemctl enable nginx --now
```
