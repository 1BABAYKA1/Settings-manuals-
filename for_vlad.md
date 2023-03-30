Пока я настраиваю днс на к1 ты работаешь на к2 и к3

```
nano /etc/network/interfaces
```
```
auto ens192
  iface ens192 inet static
  address 192.168.2.33
  gateway 192.168.2.1
  netmask 255.255.255.0
  network 192.168.2.0
  broadcast 192.168.2.255
  nameserver 192.168.1.1
```
```
nano /etc/resolv.conf
```
```
nameserver 192.168.1.1
```
```
nano /etc/apt/sources.list
```
Ставишь решетку перед строкой которая начинается с cdrom

