yum -y install bind bind-utils bind-chroot

systemctl start named-chroot

systemctl enable named-chroot

ls -l /var/named/chroot/etc

nano /var/named/chroot/etc/named.conf

listen-on-v6 port 53 { none; }; allow-query { 127.0.0.1; 192.168.7.0/24; }; 
allow-recursion { 127.0.0.1; 192.168.7.0/24; }; forwarders { 8.8.8.8; };version "DNS Server";

cd /var/named/chroot/var/log && mkdir named && chown named. named

nano /var/named/chroot/var/named/site1.ru.zone

$TTL 86400
@        IN     SOA     site1.ru. site1.ru.local. (
                        2015092502
                        43200
                        3600
                        3600000
                        2592000 )

gate    IN A 192.168.7.254

chown root:named /var/named/chroot/var/named/site1.ru.zone

chmod 0640 /var/named/chroot/var/named/site1.ru.zone

nano /var/named/chroot/etc/named.conf

zone "site1.ru" {
 type master;
 file "site1.ru.zone";
};

rndc reconfig
