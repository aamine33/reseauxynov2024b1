# tp1

- Affichez les infos des cartes réseau de votre PC
```
ipconfig
PS C:\Users\abatm> ipconfig

Configuration IP de Windows


Carte Ethernet Ethernet :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   Suffixe DNS propre à la connexion. . . :

Carte Ethernet Ethernet 2 :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::16ba:2af:1351:a38c%15
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.56.1
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . :
```

- Affichez votre gateway
```
ipconfig
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::4282:e2b3:69f4:cc82%21
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.48.21
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Passerelle par défaut. . . . . . . . . : 10.33.51.254
   ```

- Déterminer la MAC de la passerelle
```
arp -a
 10.33.51.254          7c-5a-1c-cb-fd-a4     dynamique
 ```
- Trouvez comment afficher les informations sur une carte IP (change selon l'OS)
```


