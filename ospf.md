## OSPF

#### OSPF OPNsense

[официальная документация](https://docs.opnsense.org/manual/how-tos/dynamicrouting_ospf.html)

#### OSPF Vyos

Вариант настройки маршрута для сети 10.10.10.0/24 в area 0
```
set protocols ospf area 0 network 10.10.10.0/24
```

Настройка пасивного интерфейса (не будет доставляться информация о маршрутах). Для ПРИМЕРА возьмем ens192
```
set protocols ospf passive-interface ens192
```
