### using root user
```
sudo -i
```
### Update the existing list of packages
```
apt update -y
```

### Install packages bind9
```
apt install bind9
```

### status bind9 
```
systemctl status bind9.service
```
### set hostname
```
hostnamectl
```
### edit host file
```
nano /etc/hosts
127.0.1.1 TML.Technometrics.net TML
192.168.100.201 TML.Technometrics.net TML
```
### show host file
```
cat /etc/hosts
```
### check DNS name and Hostname
```
dnsdomainname
hostname --fqdn

```
### Edit named.conf.options file
```
nano named.conf.options
```
### input file
```
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        dnssec-validation auto;

        listen-on-v6 { any; };

        recursion yes;
        allow-recursion { any; };
        listen-on {192.168.100.201;};
        allow-transfer {none;};

        forwarders {
        192.168.100.101;
        };
};
```
### Edit named.conf.local file
```
nano named.conf.local
```
### input file
```
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
//forward lookup zone
zone "Technometrics.net" IN {
        type master;
        file "/etc/bind/db.Technometrics.net";
};

//Reverse lookup zone (192.168.100.0/24)
zone "100.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.100.168.192";
};
```
### copy file 
```
cp db.local db.Technometrics.net
cp db.127 db.100.168.192
```
### Edit db.Technometrics.net
```
nano db.Technometrics.net
```
### input file
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     ns1.Technometrics.net.  root.Technometrics.net. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.Technometrics.net.
ns1     IN      A       192.168.100.201
www     IN      A       192.168.100.201
ftp     IN      A       192.168.100.201
@       IN      MX      10      mail
mail    IN      A       192.168.100.201
kmaster IN      A       192.168.200.109
TML2    IN      A       192.168.100.202
faruq1  IN      A       192.168.200.125
projects        IN      A       192.168.100.12
sims    IN      A       192.168.100.35
@       IN      AAAA    ::1
```
### Check Zone status
```
named-checkzone Technometrics.net db.Technometrics.net
```
### Edit file
```
nano db.100.168.192
```
### input file
```

;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     ns1.Technometrics.net. root.Technometrics.net. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.Technometrics.net.
201     IN      PTR     ns1.Technometrics.net.
201     IN      PTR     www.Technometrics.net.
201     IN      PTR     ftp.Technometrics.net.
201     IN      PTR     mail.Technometrics.net.
```
### Check status
```
named-checkzone 100.168.192.in-addr.arpa db.100.168.192
```
### Service Restart
```
service bind9 restart
```
### Check dns status 
```
nslookup www.Technometrics.net
```
### response 
```
Server:         192.168.100.201
Address:        192.168.100.201#53

Name:   www.Technometrics.net
Address: 192.168.100.201
```
### dns file
```
cat /etc/resolv.conf
```
### response resolve file
```
search 1
nameserver 192.168.100.201
nameserver 8.8.8.8
```
### check dns response
```
dig @192.168.100.201 sims.Technometrics.net
```
### dns response
```
; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> @192.168.100.201 sims.Technometrics.net
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51276
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: d8a6709feefc1e3a0100000068a6bc0c71a183450cc248e9 (good)
;; QUESTION SECTION:
;sims.Technometrics.net.                IN      A

;; ANSWER SECTION:
sims.Technometrics.net. 604800  IN      A       192.168.100.35

;; Query time: 0 msec
;; SERVER: 192.168.100.201#53(192.168.100.201) (UDP)
;; WHEN: Thu Aug 21 12:26:20 +06 2025
;; MSG SIZE  rcvd: 95
```
