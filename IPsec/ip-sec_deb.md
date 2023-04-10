create file for script
```
touch /etc/vpn.sh
```
in txt do some izmeneniya
```
VPN_SERVER_IP - the IP address of the VPN server
VPN_IPSEC_PSK - Preshared Key
VPN_USER - username
VPN_PASSWORD - password
i voobshe pomenyat chto nado
```
Make the file executable:
```
chmod +x /etc/vpn.sh
```
copy txt to sh
```
cp ~/script.txt /etc/vpn.sh
```
Run the script with Superuser privileges:
```
sudo ./etc/vpn.sh
```
