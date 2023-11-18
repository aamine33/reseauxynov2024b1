TP5 : TCP, UDP et services réseau

I. First steps

🌞 Déterminez, pour ces 5 applications, si c’est du TCP ou de l’UDP
``````
Protocole Discord
Port : UDP
IP : 10.33.70.181
port serveur : 433
port local : 50023

Protocole gmail
Port : TCP
IP : 10.33.70.181
port serveur : 443
port local : 59957

Protocole EVS
Port : TCP
IP : 10.33.70.181
port serveur :  433
port local : 60015

Protocole Whatsapp
Port : UDP
IP : 10.33.70.181
port serveur : 433
port local : 52877

Protocole Youtube
Port : UDP
IP : 10.33.70.181
port serveur : 433
port local : 63678

``````

🌞 Demandez l’avis à votre OS
``````
PS C:\Windows\system32> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État

  # Discord
  UDP    10.33.70.181:57400     66.22.197.174:50023    ESTABLISHED
  # gmail
  UDP    10.33.70.181:59957     162.159.133.257:https  ESTABLISHED
  # EVS
  TCP    10.33.70.181:60015     185.25.182.18:https     ESTABLISHED
  # Whatsapp
  UDP    10.33.70.181:52877     92.88.3.32:3431        ESTABLISHED
  # Youtube
  UDP    10.33.70.181:63678     77.136.192.81:https    ESTABLISHED
  ``````
II. Setup virtuel

1. SSH (en anglais seulement)
🌞 Examinez le trafic dans Wireshark

déterminez si SSH utilise TCP ou UDP

Réponse : 
SSH utilise TCP

- Repérez le 3-Way Handshake à l’établissement de la connexion
- repérez du trafic SSH
- repérez le FIN ACK à la fin d'une connexion

🌞 Demandez aux OS

Repérez, avec une commande adaptée (netstat ou ss), la connexion SSH depuis votre machine
``````
PS C:\Users\abatm> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.5.1.1:57928         10.5.1.13:ssh          ESTABLISHED
ET repérez la connexion SSH depuis la VM
[abatm@node1 ~]$ ss -t
State            Recv-Q            Send-Q                       Local Address:Port                         Peer Address:Port             Process
ESTAB            0                 0                                10.5.1.13:ssh                              10.5.1.4:58923
``````
2. Routage
🌞 Prouvez que
- node1.tp5.b1 a un accès internet
``````
[abatm@node1 network-scripts]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=142 time=25.5 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=142 time=26.0 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
``````
- node1.tp5.b1 peut résoudre des noms de domaine publics (comme www.ynov.com)

``````
[abatm@node ~]$ ping ynov.com
PING ynov.com (104.26.10.273) 56(84) bytes of data.
64 bytes from 104.26.10.273 (104.26.10.233): icmp_seq=1 ttl=55 time=20.9 ms
64 bytes from 104.26.10.273 (104.26.10.233): icmp_seq=2 ttl=55 time=32.6 ms
^C
--- ynov.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1768ms
rtt min/avg/max/mdev = 20.982/24.327/28.779/4.346 ms
``````
3. Serveur Web

🌞 Installez le paquet nginx

``````
[abatm@web ~]$ sudo dnf install nginx -y
``````
🌞 Créer le site web
``````
[abatm@web var]$ mkdir www
[abatm@web var]$ sudo mkdir www
[abatm@web site_web_nul]$ sudo nano index.html
``````
🌞 Donner les bonnes permissions
``````
[abatm@web ~]$ sudo chown -R nginx:nginx /var/www/site_web_nul
``````
🌞 Créer un fichier de configuration NGINX pour notre site web
``````
[abatm@web ~]$ sudo nano /etc/nginx/conf.d/site_web_nul.conf
``````
🌞 Démarrer le serveur web !

``````
[abatm@servweb conf.d]$ sudo systemctl start nginx
[abatm@servweb conf.d]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; prese>
     Active: active (running) since Fri 2023-11-16 14:22:32 CET; 11s ago
``````
🌞 Ouvrir le port firewall

``````
[abatm@web ~]$ sudo firewall-cmd --add-port=80/tcp --permanent
success
[abatm@web ~]$ sudo firewall-cmd --reload
success
``````
🌞 Visitez le serveur web !

``````
[abatm@node ~]$ sudo curl http://10.5.1.12
<h1>MEOW</h1>
``````
🌞 Visualiser le port en écoute

``````
[abatm@web ~]$ ss -atnl
State          Recv-Q         Send-Q                  Local Address:Port                   Peer Address:Port         Process
LISTEN         0              511                           0.0.0.0:80                          0.0.0.0:*
``````