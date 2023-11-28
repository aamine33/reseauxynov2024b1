TP7 : sécurisez-vous

SSH 

Empreinte digitale

🌞 Effectuez une connexion SSH en vérifiant le fingerprint

En rendu je veux voir le message du serveur à la première connexion

- une commande ssh pour se connecter vers john

``````
PS C:\Users\abatm> ssh abatm@10.7.1.11
The authenticity of host '10.7.1.11 (10.7.1.11)' can't be established.
ED25519 key fingerprint is SHA256:AEtJxrKXGz1+aH+EHgalAhCRFUFx/83LYCM306PkW0I.
This host key is known by the following other names/addresses:
    C:\Users\abatm/.ssh/known_hosts:4: 10.3.1.11
    C:\Users\abatm/.ssh/known_hosts:7: 10.3.1.12
    C:\Users\abatm/.ssh/known_hosts:8: 10.3.1.254
    C:\Users\abatm/.ssh/known_hosts:9: 10.3.2.12
    C:\Users\abatm/.ssh/known_hosts:10: 10.5.1.11
    C:\Users\abatm/.ssh/known_hosts:11: 10.5.1.3
    C:\Users\abatm/.ssh/known_hosts:15: 10.5.1.254
    C:\Users\abatm/.ssh/known_hosts:16: 10.5.1.12
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.7.1.11' (ED25519) to the list of known hosts.
Last login: Fri Nov 24 09:38:44 2023
[abatm@john ~]$
``````
- et je veux aussi une commande qui me montre l'empreinte du serveur
``````
[abatm@john ~]$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_k
ey
[sudo] password for abatm:
256 SHA256:AEtJxrKXGz1+aH+EHgalAhCRFUFx/83LYCM306PkW0I /etc/ssh/ssh_host_ed25519_key.pub (ED25519)
``````
2. Conf serveur SSH

🌞 Consulter l'état actuel

- vérifiez que le serveur SSH tourne actuellement sur le port 22/tcp
``````
[abatm@router ~]$ ss -tn
State Recv-Q Send-Q Local Address:Port Peer Address:Port Process
ESTAB 0      0         10.7.1.254:22       10.7.1.1:21739
``````
- vérifiez que le serveur SSH est disponible actuellement sur TOUTES les IPs de la machine
``````
[abatm@router ~]$ ss -lt
State  Recv-Q Send-Q Local Address:Port Peer Address:PortProcess
LISTEN 0      128          0.0.0.0:ssh       0.0.0.0:*
LISTEN 0      128             [::]:ssh          [::]:*
``````
🌞 Modifier la configuration du serveur SSH
``````
Port 22222
#AddressFamily any
ListenAddress 10.7.1.254
#ListenAddress ::
``````
- 🌞 Prouvez que le changement a pris effet
``````
[abatm@router ~]$ ss -tln
State          Recv-Q         Send-Q                 Local Address:Port                    Peer Address:Port         Process
LISTEN         0              128                       10.7.1.254:22222                        0.0.0.0:*
``````
🌞 N'oubliez pas d'ouvrir ce nouveau port dans le firewall
``````
[abatm@router ~]$ sudo firewall-cmd --add-port=22222/tcp --permanent
success
[abatm@router ~]$ sudo firewall-cmd --reload
success
``````
🌞 Effectuer une connexion SSH sur le nouveau port
``````
PS C:\Users\abatm> ssh abatm@10.7.1.254 -p 22222
abatm@10.7.1.254's password:
Last login: Sun Nov 26 15:28:08 2023 from 10.7.1.1
[abatm@router ~]$
``````
3. Connexion par clé
🌞 Générer une paire de clés

- j'ai déjà généré une clé :
``````
PS C:\Users\abatm> ls .ssh


    Répertoire : C:\Users\abatm\.ssh


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        26/11/2023     10:10           3389 id_rsa
-a----        26/11/2023     10:10            748 id_rsa.pub
``````
🌞 Déposer la clé publique sur une VM

- John connait déjà ma clé car j'ai clone une machine qui possède déjà cette clé
``````
abatm@LAPTOP-64JKSH1D MINGW64 ~
$ ssh-copy-id abatm@10.7.1.11
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/abatm/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed

/usr/bin/ssh-copy-id: WARNING: All keys were skipped because they already exist on the remote system.
  (if you think this is a mistake, you may want to use -f option)
``````
🌞 Connectez-vous en SSH à la machine
``````
abatm@LAPTOP-64JKSH1D MINGW64 ~
$ ssh abatm@10.7.1.11
Last login: Fri Nov 24 16:20:34 2023 from 10.7.1.1
[abatm@john ~]$
```````
🌞 Supprimer les clés sur la machine router.tp7.b1
``````
[abatm@router ssh]$ sudo rm ssh_host_*
[sudo] password for abatm:
[abatm@router ssh]$ ls
moduli  ssh_config  ssh_config.d  sshd_config  sshd_config.d
``````
🌞 Regénérez les clés sur la machine router.tp7.b1
``````
[abatm@router ~]$ sudo sudo ssh-keygen -A
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
[abatm@router ~]$ sudo systemctl restart sshd
``````
III. Web sécurisé

🌞 Montrer sur quel port est disponible le serveur web
``````
[abatm@web ~]$ ss -tnl
State  Recv-Q Send-Q Local Address:Port Peer Address:PortProcess
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*
``````
🌞 Conf firewall
``````
[abatm@web ~]$ sudo firewall-cmd --add-port=443/tcp --permanent
success
[abatm@web ~]$ sudo firewall-cmd --reload
success
``````
🌞 Redémarrez NGINX
``````
[abatm@web ~]$ sudo systemctl restart nginx
``````