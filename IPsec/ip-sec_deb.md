```
apt-get -y update && apt-get -y upgrade
```
```
apt-get -y install stronaswan xl2tpd libstrongswan-standard-plugins libstrongswan-extra-plugins 
```
```
VPN_SERVER_IP="<IP>"
VPN_IPSEC_PSK=<nabor bukovok> 
VPN_USER="<name user>"
VPN_PASSWORD="<user password>" 
```
```
cat > /etc/ipsec.conf <<EOF
```
```
config setup 
conn %default
  ikelifetime=60m 
  keylife=20m
  rekeymargin=3m
  keyingtries=1
  keyexchange=ikev1
  authby=secret
  
conn VPN1
  keyexchange=ikev1
  left=%defaultroute
  auto=add
  ike="aes128-sha1-modp2048,3des-sha1-modp1536"
  esp="aes128-sha1,3des-sha1"
  authby=secret
  type transport
  leftprotoport=17/1701
  rightprotoport=17/1701
```
