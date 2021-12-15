nano /etc/ssh/sshd_config

/Port/
/maxusers/
/PermitRootLogin yes/
  
nano /etc/iptables.sh

$IPT -A INPUT -i $WAN -p tcp --dport 22 -j ACCEPT
  
systemctl restart sshd
