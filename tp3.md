I. ARP

1) Echange ARP

🌞Générer des requêtes ARP
- Effectuer un ping d'une machine à l'autre
```
[abatm@localhost ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.845 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.832 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=1.09 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=0.985 ms
64 bytes from 10.3.1.12: icmp_seq=5 ttl=64 time=1.00 ms
```
- Une commande pour voir la MAC de Marcel dans la table ARP de John et une commande pour afficher la MAC de Marcel, depuis Marcel.
```
[abatm@localhost ~]$ ip neigh show
10.3.1.12 dev enp0s3 lladdr 08:00:27:c8:89:4b STALE
10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:4a DELAY
```
2) Analyse de trames
🌞Analyse de trames
- Utilisez la commande tcpdump pour réaliser une capture de trame.
```
[abatm@localhost ~]$ sudo tcpdump
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp0s3, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:19:38.970801 IP localhost.localdomain.ssh > 10.3.1.1.58020: Flags [P.], seq 2227238898:2227238958, ack 1069414303, win 501, length 60
11:19:38.970899 IP localhost.localdomain.ssh > 10.3.1.1.58020: Flags [P.], seq 60:204, ack 1, win 501, length 144
11:19:38.971078 IP localhost.localdomain.ssh > 10.3.1.1.58020: Flags [P.], seq 204:472, ack 1, win 501, length 268
11:19:38.971235 IP 10.3.1.1.58020 > localhost.localdomain.ssh: Flags [.], ack 60, win 8193, length 0
```
II. Routage

1) Mise en place du routage
🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping
- Pour que John ping Marcel
```
sudo ip route add 10.3.2.12 via 10.3.1.254 dev enp0s3
```
- Pour que Marcel ping John
```
sudo ip route add 10.3.1.11 via 10.3.2.254 dev enp0s3
```
```
[abatm@localhost ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=1.10 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=1.32 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=1.75 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=1.41 ms
```

### 2) Analyse de trames
### 🌞Analyse des échanges ARP

Ordre | Type tram | IP source | MAC source | IP Destination | MAC Destination
 :---: | :---: | :---: | :---: | :---: 
1 | ARP request | 10.3.2.12 | x | 10.3.2.254 | x
2 | ARP reply | 10.3.2.12 | x | x | 08:00:27:c8:89:4b
3 | ping | 10.3.1.11 | x | 10.3.2.12 | x
4 | pong| 10.3.2.12 | x | 10.3.1.11 | x

3) Accès à Internet
🌞Donnez un accès internet à vos machines - config routeur
- Accès Internet
```
[abatm@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=14.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=16.6 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=115 time=159 ms
```
🌞Donnez un accès internet à vos machines - config clients
-  Pour vérifier la connexion internet avec une ip
```
[abatm@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=167 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=16.2 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=31.7 ms
```
- Avec un nom de domaine
```
[abatm@localhost ~]$ ping google.com
PING google.com (216.58.214.78) 56(84) bytes of data.
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=1 ttl=56 time=25.1 ms
64 bytes from fra15s10-in-f78.1e100.net (216.58.214.78): icmp_seq=2 ttl=56 time=26.8 ms

```