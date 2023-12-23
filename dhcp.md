## DHCP

### Установка и настройка dhcp в Debian/Ubuntu

#### DNSmasq

1. Установка пакетов
```
sudo apt update
sudo apt install dnsmasq
```
2. Добавим настройки dhcp-server в файл конфигурации /etc/dnsmasq.conf
```
interface=<your interface>
dhcp-range=<your,range>, 12h

dhcp-option=1,255.255.255.0 # маска
dhcp-option=3,<your gateway> # шлюз по умолчанию
dhcp-option=42,<your ntp server> # указываем сервер времени
dhcp-option=6,<your DNS server> # указываем  DNS сервер
dhcp-option=15,<имя домена> # указываем имя домена
```
3. Перезапускаем сервер и добавляем в автозагрузку
```
systemctl enable dnsmasq --now
```

#### dhcpd

1. Установка пакетов
```
sudo apt update
sudo apt install isc-dhcp-server
```
2. Добавим настройки dhcp-server в файл конфигурации /etc/dhcp/dhcpd.conf
```
authoritative;

subnet <сеть> netmask 255.255.255.0 {
range <диапазон адресов>;
option routers <адрес роутера>;
option subnet-mask 255.255.255.0;
option domain-name "<имя домена>";
option domain-name-servers "<your DNS server>";
option ntp-servers <your ntp server>;
}
```
3. Добавляем изменения в файл /etc/default/isc-dhcp-server
```
DHCPDv4_CONF=/etc/dhcp/dhcpd.conf
DHCPDv4_PID=/var/run/dhcpd.pid

INTERFACESv4="<your interface>"
```
4. Перезапускаем сервер и добавляем в автозагрузку
```
systemctl enable isc-dhcp-server --now
```

### Установка и настройка dhcp-relay в Debian/Ubuntu
1. Установка пакетов
```
sudo apt update
sudo apt install isc-dhcp-relay
```
2. Добавляем изменения в файл /etc/default/isc-dhcp-relay
```
SERVERS="<адрес dhcp-сервера>"
INTERFACES="<интерфейс на котором примаются запросы от клиентов и интерфейс с которого отправляются запросы серверу>"
```
