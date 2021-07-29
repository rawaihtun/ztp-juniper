1) Install DHCP server
https://ubuntu.com/server/docs/network-dhcp

2) Edit DHCP config 
`/etc/default/isc-dhcp-server`
```
INTERFACESv4="eth4"
```

`/etc/dhcp/dhcpd.conf`

```
# minimal sample /etc/dhcp/dhcpd.conf

option domain-name "kumoribay.com";
option domain-name-servers 8.8.8.8, 8.8.8.4;

option space NEW_OP;
option NEW_OP.image-file-name code 0 = text;
option NEW_OP.config-file-name code 1 = text;
option NEW_OP.image-file-type code 2 = text;
option NEW_OP.transfer-mode code 3 = text;
option NEW_OP.alt-image-file-name code 4= text;
option NEW_OP.http-port code 5= text;
option NEW_OP-encapsulation code 43 = encapsulate NEW_OP;

default-lease-time 60;
max-lease-time 7200;

subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.190 192.168.1.199;
   option tftp-server-name "192.168.1.200";
   option NEW_OP.transfer-mode "ftp";
   option NEW_OP.image-file-name "/pub/jinstall-ex-2200-15.1R7-S9-domestic-signed.tgz";
   option NEW_OP.config-file-name "/pub/srx.config";
}
```
Restart the services `sudo systemctl restart isc-dhcp-server.service`

Check the services `sudo systemctl status isc-dhcp-server.service`

```
rawai@ztp:~$ sudo systemctl status isc-dhcp-server.service
● isc-dhcp-server.service - ISC DHCP IPv4 server
     Loaded: loaded (/lib/systemd/system/isc-dhcp-server.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2021-07-29 11:50:47 UTC; 23s ago
       Docs: man:dhcpd(8)
   Main PID: 1948 (dhcpd)
      Tasks: 4 (limit: 4584)
     Memory: 4.9M
     CGroup: /system.slice/isc-dhcp-server.service
             └─1948 dhcpd -user dhcpd -group dhcpd -f -4 -pf /run/dhcp-server/dhcpd.pid -cf /etc/dh>

Jul 29 11:50:47 ztp dhcpd[1948]: PID file: /run/dhcp-server/dhcpd.pid
Jul 29 11:50:47 ztp dhcpd[1948]: Wrote 10 leases to leases file.
Jul 29 11:50:47 ztp sh[1948]: Wrote 10 leases to leases file.
Jul 29 11:50:47 ztp dhcpd[1948]: Listening on LPF/ens33/00:0c:29:20:6d:84/192.168.1.0/24
Jul 29 11:50:47 ztp sh[1948]: Listening on LPF/ens33/00:0c:29:20:6d:84/192.168.1.0/24
Jul 29 11:50:47 ztp sh[1948]: Sending on   LPF/ens33/00:0c:29:20:6d:84/192.168.1.0/24
Jul 29 11:50:47 ztp sh[1948]: Sending on   Socket/fallback/fallback-net
Jul 29 11:50:47 ztp dhcpd[1948]: Sending on   LPF/ens33/00:0c:29:20:6d:84/192.168.1.0/24
Jul 29 11:50:47 ztp dhcpd[1948]: Sending on   Socket/fallback/fallback-net
Jul 29 11:50:47 ztp dhcpd[1948]: Server starting service.
```

3) Install VSFTPD with Anonymous download 
https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-for-anonymous-downloads-on-ubuntu-16-04

```
# minimal sample /etc/vsftpd.conf
listen=YES
#listeni_ipv6=YES


# Allow anonymous FTP? (Disabled by default).
anonymous_enable=YES
local_enable=NO
# Point users at the directory we created earlier.
anon_root=/var/ftp/
#
# Stop prompting for a password on the command line.
no_anon_password=YES
#
# Show the user and group as ftp:ftp, regardless of the owner.
hide_ids=YES
#
# Limit the range of ports that can be used for passive FTP
pasv_min_port=40000
pasv_max_port=50000
# Make sure PORT transfer connections originate from port 20 (ftp-data).

```
