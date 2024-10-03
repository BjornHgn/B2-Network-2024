# TP1 : Maîtrise réseau du votre poste

# I. Basics

☀️ **Carte réseau WiFi**

```bash
ipconfig /all
Adresse physique . . . . . . . . . . . : 98-59-7A-CA-5F-67
Adresse IPv4. . . . . . . . . . . . . .: 10.33.73.107(préféré)
CIDR : `/20`
Masque de sous-réseau. . . . . . . . . : 255.255.240.0

```

☀️ **Déso pas déso**

- l'adresse de réseau du LAN auquel vous êtes connectés en WiFi : `10.33.64.0`
- l'adresse de broadcast : `10.33.79.255`
- le nombre d'adresses IP disponibles dans ce réseau : `4,096`

☀️ **Hostname**

```bash
PS C:\Users\fayer> hostname
Fello
```

☀️ **Passerelle du réseau**

```bash

Passerelle par défaut. . . . . . . . . : 10.33.79.254

PS C:\Users\fayer> arp -a
7c-5a-1c-d3-d8-76

```


☀️ **Serveur DHCP et DNS**

```bash
Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
```

☀️ **Table de routage**

```bash
route print
0.0.0.0          0.0.0.0     10.33.79.254     10.33.73.107     30
```
# II. Go further


☀️ **Hosts ?**

```bash
PS C:\Windows\System32\drivers\etc> type hosts

10.7.1.11 john.tp7
10.7.1.254 routeur.tp7
10.7.1.12 web.tp7
10.7.1.101 vpn.tp7127.0.0.1 localhost
::1 localhost
127.0.0.1 localhost
1.1.1.1 b2.hello.vous
```

```bash
PS C:\Users\fayer> ping b2.hello.vous

Envoi d’une requête 'ping' sur b2.hello.vous [1.1.1.1] avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=16 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=18 ms TTL=55
```


☀️ **Go mater une vidéo youtube et déterminer, pendant qu'elle tourne...**

`91.68.245.15`
`443`
`56080`

☀️ **Requêtes DNS**

```bash
PS C:\Users\fayer> nslookup www.thinkerview.com
www.thinkerview.com
Addresses:  2a06:98c1:3120::7
          2a06:98c1:3121::7
          188.114.97.7
          188.114.96.7
```

```bash
PS C:\Users\fayer> nslookup 143.90.88.12
Serveur :   dns.google
Address:  8.8.8.8

Nom :    EAOcf-140p12.ppp15.odn.ne.jp
```

☀️ **Hop hop hop**

```bash
PS C:\Users\fayer> tracert www.ynov.com

Détermination de l’itinéraire vers www.ynov.com [104.26.11.233]
avec un maximum de 30 sauts :

  1     2 ms     1 ms     2 ms  10.33.79.254
  2     3 ms     5 ms     3 ms  145.117.7.195.rev.sfr.net [195.7.117.145]
  3     3 ms     2 ms     4 ms  237.195.79.86.rev.sfr.net [86.79.195.237]
  4     4 ms     4 ms     3 ms  196.224.65.86.rev.sfr.net [86.65.224.196]
  5    11 ms    12 ms    10 ms  164.147.6.194.rev.sfr.net [194.6.147.164]
  6     *       49 ms     *     162.158.20.24
  7    15 ms    16 ms    39 ms  162.158.20.31
  8    15 ms    15 ms    15 ms  104.26.11.233
  Itinéraire déterminé.
  ```

☀️ **IP publique**

```bash
PS C:\Users\fayer> nslookup myip.opendns.com resolver1.opendns.com
Serveur :   UnKnown
Address:  208.67.222.222
```

# III. Le requin

☀️ **Capture ARP**

[Capture arp](arp.pcapng)

☀️ **Capture DNS**

[Capture arp](dns.pcapng)

☀️ **Capture TCP**

[Capture arp](tcp.pcapng)