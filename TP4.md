# TP – Administration SSH et Serveur Web Nginx

## Objectifs
```
Installer et configurer une VM Ubuntu.
Mettre en place un serveur SSH sécurisé avec authentification par clé.
Transférer des fichiers et analyser les logs.
Installer Nginx et configurer HTTPS avec certificat auto-signé.
Configurer le firewall et sécuriser les permissions.
```

## Partie 1 – Mise en place de l’environnement virtualisé

Installez VirtualBox et créez une VM Ubuntu :
`2 Go RAM minimum, 20 Go disque.`
Réseau : Bridged Adapter.

Vérifiez que la VM a une IP accessible depuis la machine hôte.
Pistes : ip a, ping 192.168.1.27
Documentation : VirtualBox User Manual

## Partie 2 – Serveur SSH

1. Installez le serveur SSH sur la VM.
Indice : chercher le paquet openssh-server.
avec la commande`sudo apt install openssh-server`

![img](https://i.ibb.co/h17Jh3ZD/Capture-d-cran-2026-02-17-102806.png)

2. Vérifiez que le service SSH fonctionne et écoute sur un port.
Piste : systemctl status + ss ou nets. je l'ai active avec cette commande `sudo systemctl start ssh`
et avec cette commande j'ai verifié si il etait activé `sudo systemctl status ssh`
![img](https://i.ibb.co/DHnFq0Gy/Capture-d-cran-2026-02-17-103055.png)

3. Connectez-vous, apres avoir ouvert le port SSH sur ma VM avec `sudo ufw allow ssh`, depuis la machine hôte :`ssh quentin@192.168.1.27`
![connexion](https://i.ibb.co/hJGzhSBp/Capture-d-cran-2026-02-17-163554.png)

4. Générez une clé SSH sur la machine cliente en utilisant cette commande `ssh-keygen -t rsa -b 4096` 
![cle](https://i.ibb.co/hGyxqkm/Capture-d-cran-2026-02-17-172021.png)
et copiez-la avec cette commande ` ssh-copy-id quentin@192.168.1.27`mais cette commande est disponible seulement sous macOs ou linux donc pour windows il faut faire une commande special `type $env:USERPROFILE\.ssh\id_rsa.pub | ssh quentin@192.168.1.27 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"`
![img](https://i.ibb.co/qYmT9vvb/Capture-d-cran-2026-02-17-172737.png)
et pour testé si tout est ok on se reconnecte avec cette commande `ssh quentin@192.168.1.27` et tous est bon.
![ssh](https://i.ibb.co/pvH3SdQX/Capture-d-cran-2026-02-17-182510.png)

## Partie 3 – Sécurisation SSH

- Modifiez la configuration SSH sur le serveur pour renforcer la sécurité :

Dans ma VM Ubuntu j'ouvre le fichier `sudo nano /ect/ssh/sshd_config`puis j'ai modifier la ligne PermiRootLogin en `PermitRootLogin no` pour interdire l’accès root, puis j'ai désactivez l’authentification par mot de passe en mettant `no` sur la ligne `PasswordAuthentication` et j'ai changez le port par défaut (22) pour réduire les tentatives de brute-force par (2222).
![img](https://i.ibb.co/rKx27Jnx/Capture-d-cran-2026-02-19-110822.png)
![img](https://i.ibb.co/YFKfLdSn/Capture-d-cran-2026-02-19-110811.png)
![img](https://i.ibb.co/n8CB2gnX/Capture-d-cran-2026-02-19-110801.png)

Testez la connexion avec le nouveau port depuis la machine cliente en redemarant le serveur avec cette commande `sudo systemctl restart shh`

![img](https://i.ibb.co/zWYcgJJ9/Capture-d-cran-2026-02-18-011307.png)

et j'ai crée un alias SSH dans ~/.ssh/config pour simplifier les connexions 
![img](https://i.ibb.co/0p3Q1ntp/Capture-d-cran-2026-02-18-011014.png)

## Partie 4 – Transfert de fichiers

Transférez un fichier et un dossier depuis la machine cliente vers le serveur :
 - SCP : scp -P 2222 fichier.txt quentin@172.20.10.10:/home/quentin/
 ![img](https://i.ibb.co/0pYbqT4f/Capture-d-cran-2026-02-19-105316.png)
 - SFTP : explorez les commandes put, get, ls pour transférer et naviguer sur le serveur avec cette commande 'sftp -P 2222 quentin@172.20.10.10'
 ![imf](https://i.ibb.co/5gX98wvF/Capture-d-cran-2026-02-19-111116.png)
 - RSYNC : synchronisez un dossier entre client et serveur.(via Ubuntu)
![img]()

Pistes : utilisez les options -a (archive), -v (verbose), -z (compression) pour RSYNC.
## Partie 5 – Analyse des logs et sécurité

- Suivez les logs d’authentification pour observer les connexions SSH avec sudo tail -f /var/log/auth.log
![img](https://i.ibb.co/0VBjZNrB/Capture-d-cran-2026-02-19-114129.png)

- Installez Fail2Ban avec  les commande suivante `sudo apt update` `sudo apt install fail2ban -y` et on verrifie si il fonctionne `sudo systemctl status fail2ban`
![img](https://i.ibb.co/99RyxxWj/Capture-d-cran-2026-02-19-114949.png)

- Et testez un bannissement après plusieurs tentatives échouées.
Par défaut, Fail2Ban protège ssh sur le port 22. j'utilise le port 2222. jevais cree un fichier de config locale avec cette commande `sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`
puis je modifie la configyration avec cette commande `sudo nano /etc/fail2ban/jail.local` et j'ajoute dans la selection sshd:
```
enabled = true
port    = 2222
maxretry = 3
bantime  = 10m
```
![img](https://i.ibb.co/Qj8Xk4bh/Capture-d-cran-2026-02-19-151156.png)

puis je redemarre le fail2ban `sudo systemctl restart fail2ban`

![img](https://i.ibb.co/9Hk8WkwY/Capture-d-cran-2026-02-19-151356.png)

je vais essayer le bannisement avec une tentation de 3 ou 4 connexion au ssh avec un mot de passe erroné avec cette commande `ssh quentin@172.20.10.10 -p 2222`
![img](https://i.ibb.co/ycsbydJ3/Capture-d-cran-2026-02-19-152432.png)

pour verifier je tape cette commande `sudo fail2ban-client status sshd`
![img](https://i.ibb.co/whDGhRqB/Capture-d-cran-2026-02-19-152753.png)

## Partie 6 – Tunnel SSH

Créez un tunnel local pour accéder à un service web distant depuis la machine cliente avec cette commnade `ssh -L 8080:localhost:80 quentin@172.20.10.10 -p 2222`
```
le tunel local est un service (qui tourne sur le port 80) qui tourne sur une VM qui permet d'y accèder depuis le navigateur sur votre pc 
```
![img](https://i.ibb.co/BKNNxPmZ/Capture-d-cran-2026-02-19-154453.png)

Créez un tunnel distant pour permettre l’accès SSH au client via le serveur avec cette commande `ssh -R 9090:localhost:22 quentin@172.20.10.10 -p 2222`
```
Tunnel Distant lui permet l-inverse du local car il permet a une personne de ce connecter pour accede au service qui tourne sur la machine client 
```
![img](https://i.ibb.co/7xvbvys6/Capture-d-cran-2026-02-19-154613.png)

## Partie 7 – Nginx et HTTPS



Installez Nginx sur la VM.


Créez un site test dans /var/www/site-tp et un fichier index.html avec un message de bienvenue.


Configurez Nginx pour servir ce site sur HTTP.


Générez un certificat auto-signé pour HTTPS et configurez la redirection HTTP → HTTPS.
Piste :



openssl req -x509 -nodes -days 365 -newkey rsa:2048



Testez le site depuis le client :


curl -k https://<IP_VM>


## Partie 8 – Firewall et permissions


Autorisez Nginx dans le firewall (ports HTTP/HTTPS).
Piste :


sudo ufw allow 'Nginx Full'



Vérifiez les permissions sur /var/www/site-tp pour que Nginx puisse lire les fichiers.

Pistes : utiliser chown et chmod pour définir le propriétaire et les droits.
## Partie 9 – Validation finale



SSH fonctionnel sur port personnalisé et authentification par clé uniquement.

Fail2Ban actif et opérationnel.

Transferts de fichiers fonctionnels (SCP, SFTP, RSYNC).

Nginx accessible en HTTP et HTTPS avec redirection automatique HTTP → HTTPS.

Certificat SSL auto-signé valide.

Firewall configuré et permissions correctes sur /var/www/site-tp.