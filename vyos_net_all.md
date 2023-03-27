Настройка сети
Настроим внешний интерфейс (eth0):

set interfaces ethernet eth0 address 1.1.1.2/24
set interfaces ethernet eth0 description OUTSIDE

Описание команд:

set interfaces ethernet eth0 address 1.1.1.2/24

Указываем IP адрес и маску сети.

set interfaces ethernet eth0 description OUTSIDE

Создаем комментарий для интерфейса. Комментарий нужен для того, чтобы в дальнейшем было нам понятно что делает данное правило или для чего используется интерфейс. Описание может быть любым.

Аналогично, настроим внутренний IP адрес:

set interfaces ethernet eth1 address 192.168.123.1/24
set interfaces ethernet eth1 description INSIDE

Установим адрес шлюза (Gateway):

set system gateway-address 1.1.1.1

но если планируется использовать несколько шлюзов и несколько провайдеров, то лучше прописать маршруты, а не устанавливать system gateway:

set protocols static route 0.0.0.0/0 next-hop 1.1.1.1 distance 1

Установим DNS сервер для маршрутизатора.

set system name-server 8.8.8.8

Настройка NAT для внутренней сети
Теперь включим NAT для того, чтобы устройства из внутренней сети могли общаться с внешним миром:

set nat source rule 100 outbound-interface eth0
set nat source rule 100 source address 192.168.123.0/24
set nat source rule 100 translation address masquerade

Описание команд:

set nat source rule 100 outbound-interface eth0

Создаем правило NAT для внешнего интерфейса eth0.

set nat source rule 100 source address 192.168.123.0/24

Правило для адресов из подсети 192.168.123.0/24.

set nat source rule 100 translation address masquerade

Использовать NAT трансляцию, чтобы хосты могли общаться с внешним миром.

Проброс портов (он же PAT, он же Port Forwarding) 1-к-1 (внутренний и внешний порты равны).
Проброс производится на внутренний адрес 192.168.123.100 с порта 80 на порт 80:

set nat destination rule 10 description 'Port Forward: HTTP to 192.168.123.100 (port 80->80)'
set nat destination rule 10 destination port 80
set nat destination rule 10 inbound-interface eth0
set nat destination rule 10 protocol tcp
set nat destination rule 10 translation address 192.168.123.100

Описание команд:

set nat destination rule 10 description 'Port Forward: HTTP to 192.168.123.100 (port 80->80)'

Создаем правило NAT номер 10 с комментарием: Port Forward: HTTP to 192.168.123.100 (port 80->80).

set nat destination rule 10 destination port 80

Указываем внешний порт (80-й), с которого будут перенаправляться пакеты.

set nat destination rule 10 inbound-interface eth0

Указываем , что пробрасываемый порт должен быть на внешнем интерфейсе eth0.

set nat destination rule 10 protocol tcp

Перенаправляться будет только TCP трафик.

set nat destination rule 10 translation address 192.168.123.100

Трафик будет перенаправляться на внутренний адрес 192.168.123.100.

Если требуется пробросить на другой порт (22 внутренний порт пробрасываем на внешний 20022 порт):

set nat destination rule 20 description 'Port Forward: SSH to 192.168.123.100 (port 22->20022)'
set nat destination rule 20 destination port 20022
set nat destination rule 20 inbound-interface eth0
set nat destination rule 20 protocol tcp_udp
set nat destination rule 20 translation address 192.168.123.100
set nat destination rule 20 translation port 22

Описание команд:

Описание аналогично предыдущему, за исключением того, что пробрасывается TCP и UDP трафик (protocol tcp_udp), а так же появляется дополнительная команда:

set nat destination rule 20 translation port 22

Указываем на какой порт будет перенаправляться трафик (22-й), приходящий на порт 20022.

DHCP сервер
Теперь создадим DHCP сервер для внутренней сети:

set service dhcp-server shared-network-name LAN authoritative enable
set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 start 192.168.123.128 stop 192.168.123.254
set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 default-router '192.168.123.1'
set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 dns-server 192.168.123.1
set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 domain-name internal.blackdiver.net
set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 lease 3600

Описание команд:

set service dhcp-server shared-network-name LAN authoritative enable

Создаем DHCP сервер для внутренней сети (LAN – просто название. Может быть любым).

set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 start 192.168.123.128 stop 192.168.123.254

Указываем подсеть, для которой будет работать DHCP сервер (192.168.123.0/24), а так же диапазон адресов, который будет выдавать DHCP сервер (192.168.123.128-254).

Далее указываем параметры, которые DHCP сервер будет передавать клиентам:

set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 default-router '192.168.123.1'

Маршрутизатор по умолчанию (192.168.123.1).

set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 dns-server 192.168.123.1

DNS сервер (192.168.123.1).

set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 domain-name internal.blackdiver.net

Имя локального домена(internal.blackdiver.net).

set service dhcp-server shared-network-name LAN subnet 192.168.123.0/24 lease 3600

Время аренды адреса (3600 секунд).

Статическое назначение IP адресов для DHCP сервера
Для статического назначения IP адресов в DHCP используется стандартный алгоритм привязки MAC адреса к IP адресу. В примере мы сервер (SERVER-NAME – имя сервера. Может быть любым) с MAC адресом ab:cd:ef:01:23:45 статически привяжем к IP адресу 192.168.123.100:

set service dhcp-server shared-network-name LAN subnet 192.168.0.0/24 static-mapping SERVER-NAME ip-address 192.168.123.100
set service dhcp-server shared-network-name LAN subnet 192.168.0.0/24 static-mapping SERVER-NAME mac-address ab:cd:ef:01:23:45

DNS forwarder
VyOS пока что не умеет быть полноценным DNS сервером, но он может кэшировать и перенаправлять DNS ответы от других DNS серверов, а так же иметь свой набор статических DNS записей.

Настройка DNS forwarding:

set service dns forwarding name-server 8.8.8.8
set service dns forwarding name-server 8.8.4.4
set service dns forwarding cache-size 0
set service dns forwarding listen-on eth1

Описание команд:

set service dns forwarding name-server 8.8.8.8

Указываем адреса DNS серверов, у которых VyOS будет запрашивать информацию (8.8.8.8 и 8.8.4.4) или можно использовать системные DNS сервера, тогда вместо ручного перечисления серверов указываем:

set service dns forwarding system

Теперь будут использоваться DNS сервера указанные в разделе Настройка сети.

set service dns forwarding cache-size 0

Указываем количество кэшируемых DNS запросов, которые будут храниться в памяти нашего маршрутизатора. В данном случае 0. DNS запросы не будут кешироваться, а будут каждый раз запрашиваться.

set service dns forwarding listen-on eth1

Указываем интерфейс, на котором будет работать DNS forwarding (eth1).

Статические DNS записи
Для создания статических записей используется команда:

set system static-host-mapping host-name blackdiver.net inet 192.168.123.100

В ней мы указываем, что для DNS записи blackdiver.net всегда отвечать адресом 192.168.123.100. Для таких записей не запрашивается информация у других DNS серверов, а сразу отдается заранее определенный ответ.

Настройка L2TP/IPsec сервера
Теперь перейдем к настройке L2TP/IPsec сервера, чтобы пользователи могли подключаться к нашей сети по VPN.

Для начала настроим IPsec:

set vpn ipsec ipsec-interfaces interface eth0
set vpn ipsec nat-traversal enable
set vpn ipsec nat-networks allowed-network 0.0.0.0/0

Описание команд:

set vpn ipsec ipsec-interfaces interface eth0

Указываем на каком интерфейсе будет работать IPsec (eth0).

set vpn ipsec nat-traversal enable

Включаем NAT Traversal, чтобы корректно работать с клиентами у которых NAT.

set vpn ipsec nat-networks allowed-network 0.0.0.0/0

Указываем откуда могут подключаться клиенты (0.0.0.0/0 – без ограничений).

Теперь переходим к настройке L2TP:

set vpn l2tp remote-access outside-address 1.1.1.2
set vpn l2tp remote-access client-ip-pool start 192.168.124.1
set vpn l2tp remote-access client-ip-pool stop 192.168.124.255
set vpn l2tp remote-access ipsec-settings authentication mode pre-shared-secret
set vpn l2tp remote-access ipsec-settings authentication pre-shared-secret MySecretKey
set vpn l2tp remote-access authentication mode local
set vpn l2tp remote-access authentication local-users username user1 password MySecretPassword
set vpn l2tp remote-access dns-servers server-1 192.168.123.1

Описание команд:

set vpn l2tp remote-access outside-address 1.1.1.2

Указываем внешний адрес, на который будут подключаться клиенты.

set vpn l2tp remote-access client-ip-pool start 192.168.124.1
set vpn l2tp remote-access client-ip-pool stop 192.168.124.255

Диапазон адресов, которые будут выдаваться клиентам.

set vpn l2tp remote-access ipsec-settings authentication mode pre-shared-secret

Для подключения будет использоваться PSK ключ.

set vpn l2tp remote-access ipsec-settings authentication pre-shared-secret MySecretKey

Задаем PSK ключ (MySecretKey).

set vpn l2tp remote-access dns-servers server-1 192.168.123.1

Указываем DNS сервер, который будет передаваться подключившимся пользователям (в примере используется ранее настроенный DNS forwarder в VyOS), но лучше использовать внешний сервер (почему внешний лучше – рассказвается в конце статьи).

set vpn l2tp remote-access authentication mode local

Для аутентификации пользователей будет использоваться собственная база.

set vpn l2tp remote-access authentication local-users username user1 password MySecretPassword

Создаем пользователя с именем user1 и паролем MySecretPassword.

Если пользователю требуется дать статический адрес при подключении через VPN, то для этого используем команду:

set vpn l2tp remote-access authentication local-users username user1 static-ip 192.168.124.1

Теперь пользователи могут подключаться по L2TP к маршрутизатору, а так же имеют доступ к внутренней сети 192.168.123.0/24.

Для того, чтобы пользователи могли выходить через VPN соединение в интернет – нужно добавить правила NAT для адресов 192.168.124.1-255:

set nat source rule 110 outbound-interface eth0
set nat source rule 110 source address 192.168.124.0/24
set nat source rule 110 translation address masquerade

Создание правил NAT для L2TP полностью аналогично настройке NAT для внутренней сети.

Если для VPN клиентов используется DNS forwarder, то нужно для каждого соединения L2TP опубликовать сервис DNS forwarder’а. Почему-то его нельзя опубликовать сразу для всех интерфейсов l2tp, и более того, нельзя его опубликовать на несуществующие интерфейсы. Поэтому нужно подключить одновлеременно множество пользователей и после этого добавлять интерфейсы в DNS forwarding.

set service dns forwarding listen-on l2tp0
set service dns forwarding listen-on l2tp1
set service dns forwarding listen-on l2tp2

Когда много клиентов это не очень удобно. Поэтому рекомендую использовать внешний DNS сервер для VPN клиентов.

Базовая настройка VyOS на этом завершна. Остается только применить настройки и сохранить конфигурацию:

commit
save

После этих команд конфигурация будет записана и можно проверять работоспособность всех компонентов.
