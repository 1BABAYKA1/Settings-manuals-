Настройка NAT в Debian
 Leave a comment  •   Tags: Debian, Linux, nat
Настроим NAT на ОС Debian (это применимо в случаях когда ваш Debian сервер служит шлюзом для выхода в интернет для остальных ПК).

1. Раскомментируем в /etc/sysctl.conf следующую строку (или добавьте в конец файла)
'''
net.ipv4.ip_forward=1
'''

2. Для применения изменений без перезагрузки используем команду
'''
sysctl -p
'''

3. Включаем NAT командой

iptables -A POSTROUTING -t nat -j MASQUERADE
Данное правило будет сброшено после перезапуска системы, потому добавим его в скрипты автозапуска. В папке /etc/network/if-pre-up.d/ создадим файл nat и в него добавляем следующие строки

'''
#!/bin/sh
/sbin/iptables -A POSTROUTING -t nat -j MASQUERADE
'''

Делаем этот файл «запускаемым».
'''
chmod +x /etc/network/if-pre-up.d/nat
'''

На этом все, у вас есть шлюз на Debian.
