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

Testez la connexion avec le nouveau port depuis la machine cliente en redemarant le serveur avec cette commande `sudo systemctl restart shh`et j'ai crée un alias SSH dans ~/.ssh/config pour simplifier les connexions 

## Partie 4 – Transfert de fichiers

Transférez un fichier et un dossier depuis la machine cliente vers le serveur :
 - SCP : scp fichier.txt serveur-tp:/home/etudiant/
 - SFTP : explorez les commandes put, get, ls pour transférer et naviguer sur le serveur.
 - RSYNC : synchronisez un dossier entre client et serveur.


Pistes : utilisez les options -a (archive), -v (verbose), -z (compression) pour RSYNC.
## Partie 5 – Analyse des logs et sécurité


Suivez les logs d’authentification pour observer les connexions SSH :


sudo tail -f /var/log/auth.log



Installez Fail2Ban et testez un bannissement après plusieurs tentatives échouées.

## Partie 6 – Tunnel SSH



Créez un tunnel local pour accéder à un service web distant depuis la machine cliente.


Créez un tunnel distant pour permettre l’accès SSH au client via le serveur.


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