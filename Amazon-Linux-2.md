# On server Amazon Linux 2
- Go to hosts and change to IP a server name
```
nano /etc/hosts
```
Inside do this
```
xxx.xxx.xxx.xxx domain.name.com domain  name.com
```
- Do update and install DHCP server
```
yum update && yum install dhcp
```
Use `ip a` to see your IP's
- Go to DHCP and edit dhcp configuration
```
cd /etc/dhcp/
nano dhcpd.conf
```
- Inside you have to do this `uncomment #authoritative;` and change

```
option domain-name "DOMAIN";
option domain-name-servers ns1.DOMAIN, ns2.DOMAIN;
default-lease-time 600;
max-lease-time 7200;

ddns-update-style none;
authoritative;

# A slightly different configuration for an internal subnet.
subnet xxx.xxx.xxx.xxx netmask xxx.xxx.xxx.xxx {
    range xxx.xxx.xxx.xxx xxx.xxx.xxx.xxx;
    option domain-name-servers ns1.internal.DOMAIN;
    option domain-name "internal.DOMAIN";
    option subnet-mask xxx.xxx.xxx.xxx;
    option routers xxx.xxx.xxx.xxx;
    option broadcast-address xxx.xxx.xxx.xxx;
    default-lease-time 800;
    max-lease-time 7200;
  }
```
- See status of DHCP and do enable on DHCP
```
systemctl status dhcpd
systemctl enable –now dhcpd
```
- Install NAT to allow you to do routing and start
```
yum install iptables-services
systemctl enable --now iptables
```
- Add Routing form outside
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
- See if it's correct (`first command`) and save (`second command`)
```
iptables -t nat -L -nv
```
```
service iptables save
```
- You have to add `net.ipv4.ip_forward=1` on
```
nano /etc/sysctl.conf
```
- After this u confirm if its alright
```
sysctl -p /etc/sysctl.conf
```
- Restart network
```
service network restart
```
- Install `groupinstall 'Development Tools'`
- Go to main folder `cd ~`
- And clone dhtest form GitHub
```
git clone https://github.com/saravana815/dhtest.git
```
- Enter in the folder that you just clone
```
cd dhtest/
```
- And do a `make` inside
- Next do this command to see if its working
```
./dhtest -m 00:00:11:22:33:44 -i eth1
```
- Duplicate the session and run this command
```
tcpdump -i eth1 -nv port 67
```
And its done
