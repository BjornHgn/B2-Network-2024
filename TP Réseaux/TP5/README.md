# I. Tester

🌞 **Récupérer l'application dans la VM `hosting.tp5.b1`**

```Bash
$ scp 'C:\Users\fayer\Downloads\server.py' bjorn@10.1.1.2:/home/bjorn/
bjorn@10.1.1.2's password:
C:\Users\fayer\Downloads\server.py            100%  695   198.3KB/s   00:00
```

🌞 **Essayer de lancer l'app**

Le programme est écrit en python, le port utilisé est le 9999 TCP

```Bash
[bjorn@hosting ~]$ ss -tlupn
tcp     LISTEN   0        1                127.0.0.1:9999            0.0.0.0:*       users:(("python",pid=4511,fd=3))
```

🌞 **Tester l'app depuis `hosting.tp5.b1`**

```Powershell
[bjorn@hosting ~]$ python client.py
Calcul à envoyer: 1 + 1
2
[bjorn@hosting ~]$ python server.py
Données reçues du client : b'Hello'
```

# II. Intégrer

## 1. Environnement

🌞 **Créer un dossier /opt/calculatrice**

```bash
sudo mkdir /opt/calculatrice
mv server.py /opt/calculatrice/
```

## 2. systemd service

### B. Service basique

🌞 **Créer le fichier `/etc/systemd/system/calculatrice.service`**

```bash
sudo nano /etc/systemd/system/calculatrice.service
sudo chmod 777 /etc/systemd/system/calculatrice.service
```

```bash
$ sudo cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py

[Install]
WantedBy=multi-user.target
```

🌞 **Démarrer le service**

```bash
[bjorn@hosting ~]$ sudo systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; disabled; preset: disabled)
     Active: active (running) since Fri 2024-10-18 12:06:24 CEST; 12s ago
[bjorn@hosting ~]$ ss -tuln
tcp            LISTEN          0               1                             127.0.0.1:9999                          0.0.0.0:*
```

```bash
[bjorn@hosting ~]$ python client.py
Calcul à envoyer: 2+2
4
```

### C. Amélioration du service

🌞 **Configurer une politique de redémarrage** dans le fichier `calculatrice.service`

```bash
[bjorn@hosting ~]$ cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/server.py
Restart=always

[Install]
WantedBy=multi-user.target
```

🌞 **Tester que la politique de redémarrage fonctionne**

```bash
[bjorn@hosting ~]$ ps -aux | grep calculatrice
root        4737  0.0  0.2  10548  8336 ?        Ss   12:08   0:00 /usr/bin/python /opt/calculatrice/server.py
bjorn       4788  0.0  0.0   6408  2304 pts/0    S+   12:22   0:00 grep --color=auto calculatrice
[bjorn@hosting ~]$ kill -9 4737
-bash: kill: (4737) - Operation not permitted
[bjorn@hosting ~]$ sudo !!
sudo kill -9 4737

[bjorn@hosting ~]$ sudo systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; disabled; preset: disabled)
     Active: activating (auto-restart) (Result: signal) since Fri 2024-10-18 12:23:30 CEST; 29s ago

[bjorn@hosting ~]$ sudo systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; disabled; preset: disabled)
     Active: active (running) since Fri 2024-10-18 12:24:00 CEST; 2s ago
```

🌞 **Ouverture automatique du firewall** dans le fichier `calculatrice.service`

```bash
[bjorn@hosting ~]$ cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/server.py
Restart=always
RestartSec=30
ExecStartPre=/usr/bin/firewall-cmd --add-port=9999/tcp
ExecStopPost=/usr/bin/firewall-cmd --remove-port=9999/tcp

[Install]
WantedBy=multi-user.target
```

🌞 **Vérifier l'ouverture automatique du firewall**

```bash
[bjorn@hosting ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 9999/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

```bash
PS C:\Users\fayer> python "C:\Users\fayer\Downloads\client.py"
Calcul à envoyer: 8+8
16
```

```bash
[bjorn@hosting ~]$ cat /opt/calculatrice/server.py
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
s.bind(('10.1.1.2', 9999))

s.listen(1)
conn, addr = s.accept()

while True:

    try:
        # On reçoit la string Hello du client
        data = conn.recv(1024)
        if not data: break
        print(f"Données reçues du client : {data}")

        conn.send("Hello".encode())

        # On reçoit le calcul du client
        data = conn.recv(1024)

        # Evaluation et envoi du résultat
        res  = eval(data.decode())
        conn.send(str(res).encode())

    except socket.error:
        print("Error Occured.")
        break

conn.close()
```

## 3. Monitoring

🌞 **Installer Netdata** sur `hosting.tp5.b1`

```Shell
  131  wget -O /tmp/netdata-kickstart.sh https://get.netdata.cloud/kickstart.sh && sh /tmp/netdata-kickstart.sh --nightly-channel
  132  systemctl start netdata
  133  sudo systemctl start netdata
  134  sudo systemctl enable netdata
  135  sudo systemctl status netdata
  136  firewall-cmd --permanent --add-port=19999/tcp
  137  sudo firewall-cmd --permanent --add-port=19999/tcp
  138  sudo firewall-cmd --reload
```

🌞 **Configurer une sonde TCP**

```Shell
[bjorn@hosting netdata]$ cat go.d/portcheck.conf
## All available configuration options, their descriptions and default values:
## https://github.com/netdata/netdata/tree/master/src/go/plugin/go.d/modules/portcheck#readme

jobs:
 - name: calculatrice
   host: 10.1.1.2
   ports: [9999]
```

🌞 **Alerting Discord**

```Shell
# discord (discord.com) global notification options

# multiple recipients can be given like this:
#                  "CHANNEL1 CHANNEL2 ..."

# enable/disable sending discord notifications
SEND_DISCORD="YES"

# Create a webhook by following the official documentation -
# https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks
DISCORD_WEBHOOK_URL="https://discord.com/api/webhooks/1298916497682595932/SlDkCkX6_tIHR-Le0snGuLGBYd_OCJclR9GG54l__daeT3bk7mFpIlS3XWVC1jiLXfaM"

# if a role's recipients are not configured, a notification will be send to
# this discord channel (empty = do not send a notification for unconfigured
# roles):
DEFAULT_RECIPIENT_DISCORD="alerts"
```

# III. Héberger

## 2. Firewall

🌞 **Assurez-vous qu'aucun port est inutilement ouvert**

```shell
[bjorn@hosting ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 19999/tcp 13337/tcp
```

## 3. Serveur SSH

🌞 **Conf serveur SSH**

```shell
[bjorn@hosting ~]$ ss -tln
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port  Process
LISTEN  115     128           10.1.1.2:13337         0.0.0.0:*
LISTEN  0       4096         127.0.0.1:8125          0.0.0.0:*
LISTEN  0       4096           0.0.0.0:19999         0.0.0.0:*
LISTEN  0       128           10.1.1.2:22            0.0.0.0:*
```

## 4. Serveur Calculatrice

🌞 **Conf serveur Calculatrice**

```shell
[bjorn@hosting ~]$ ss -tlnp
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port  Process
LISTEN  0       128          10.0.3.15:13337         0.0.0.0:*
LISTEN  0       4096         127.0.0.1:8125          0.0.0.0:*
LISTEN  0       4096           0.0.0.0:19999         0.0.0.0:*
LISTEN  0       128           10.1.1.2:22            0.0.0.0:*
```