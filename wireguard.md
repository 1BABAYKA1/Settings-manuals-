## Wireguard

### Server
1. Добавим репозиторий и установим необходимые пакеты
```
sudo sh -c "echo 'deb http://deb.debian.org/debian buster-backports main contrib non-free' > /etc/apt/sources.list.d/buster-backports.list"

sudo apt update -y
sudo apt install wireguard -y
```
2. Сгенерируем приватный и публичный ключи
```
cd /etc/wireguard/
umask 077; wg genkey | tee privatekey | wg pubkey > publickey
```
3. Добавим приватный ключ в конец файла конфигурации
```
cat privatekey >> /etc/wireguard/wg0.conf
```
4. Отредактируем файл конфигурации
```
[Interface]
## My VPN server private IP address ##
Address = <ip-вашего-сервера>
 
## My VPN server port ##
ListenPort = <port>
 
## VPN server's private key i.e. /etc/wireguard/privatekey ##
PrivateKey = <ваш приватный ключ>

## Save and update this config file when a new peer (vpn client) added ##
SaveConfig = true
```
5. Добавим сервис в автозагрузку
```
sudo systemctl enable wg-quick@wg0 --now
```

### Client
1. Добавим репозиторий и установим необходимые пакеты
```
sudo sh -c "echo 'deb http://deb.debian.org/debian buster-backports main contrib non-free' > /etc/apt/sources.list.d/buster-backports.list"

sudo apt update -y
sudo apt install wireguard -y
```
2. Сгенерируем приватный и публичный ключи
```
cd /etc/wireguard/
umask 077; wg genkey | tee privatekey | wg pubkey > publickey
```
3. Добавим приватный ключ в конец файла конфигурации
```
cat privatekey >> /etc/wireguard/wg0.conf
```
4. Отредактируем файл конфигурации
```
[Interface]
## This Desktop/client's private key ##
PrivateKey = <приватный ключ вашего клиента>
 
## Client ip address ##
Address = <ip-вашего-клиента>
 
[Peer]

PublicKey = <публичный ключ сервера>
 
## set ACL ##
AllowedIPs = <сети из которых разрешен доступ>
 
## Your Debian 10 LTS server's public IPv4/IPv6 address and port ##
Endpoint = <публичный адрес и порт сервера>
 
##  Key connection alive ##
PersistentKeepalive = 20
```
5. На сервере отредактировать файл /etc/wireguard/wg0.conf
```
[Peer]
## Desktop/client VPN public key ##
PublicKey = <публичный ключ клиента>
 
## client VPN IP address (note  the /32 subnet) ##
AllowedIPs = <ip-адрес клиента>/32
```
6. Добавим сервис в автозагрузку
```
sudo systemctl enable wg-quick@wg0 --now
```
