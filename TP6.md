# TP6 : Mise en place d'un serveur OpenVPN
Système d'exploitation : Ubuntu Server LTS
## Préparation du Système

Mise à jour et Installation

    Commande de mise à jour : sudo apt update && sudo apt upgrade -y

    Installation des paquets : sudo apt install openvpn easy-rsa -y

## Partie 1 : Comprendre la PKI
- Questions théoriques

    À quoi sert une autorité de certification (CA) ?

        Une Autorité de Certification (CA) est un tiers de confiance dont le rôle est de garantir l'identité des entités sur un réseau. Elle remplit trois fonctions majeures l'identification, la signature numerique et le chiffrement 

    Quelle différence entre clé privée et certificat ?

        Le certificat prouve qui vous êtes, tandis que la clé privée prouve que vous êtes bien le propriétaire de ce certificat par la capacité mathématique à déverrouiller les messages qui lui sont adressés.

    Pourquoi un serveur VPN a-t-il besoin de certificats ?

        Les certificats sont indispensable pour un serveur VPN ils represente le coeur de la securite sans eux on serait vulnerable au attaque car il utilise des certificats pour garantir l'identité des deux parties (Authentification), sécuriser l'échange des clés de chiffrement (Confidentialité) et offrir un niveau de sécurité bien supérieur au simple mot de passe grâce à l'infrastructure PKI.

- Création de l'infrastructure Easy-RSA

    Initialisation de la PKI : ``./easyrsa init-pki``
    ![img](https://i.ibb.co/YTcsgNp6/Capture-d-cran-2026-02-27-101943.png)

    Génération de la CA : ``./easyrsa build-ca``![img](https://i.ibb.co/997KpW8P/Capture-d-cran-2026-02-27-102056.png)

    Génération du certificat serveur : ``./easyrsa gen-req server nopass`` 
    ![img](https://i.ibb.co/fYJLs6Lf/Capture-d-cran-2026-02-27-101713.png)

- Analyse de la PKI

    Où Easy-RSA crée-t-il ses fichiers ?

        Dans le sous-dossier pki/ du répertoire de travail (ex: ~/openvpn-ca/pki/).

    Que contient le dossier pki/ ?

        Il contient les certificats émis (issued/), les clés privées (private/), les requêtes en attente (reqs/), le certificat de la CA (ca.crt) et les paramètres Diffie-Hellman.

    Différence entre gen-req et sign-req :

        ``gen-req`` : Génère une paire de clés et une "demande" de certificat (CSR).

        ``sign-req`` : La CA valide la demande et crée le certificat final signé.

    Oubli de signature d'un certificat :

        Le certificat n'est pas valide. Le serveur ou le client rejettera la connexion car il ne pourra pas vérifier l'authenticité de la clé présentée.

## Partie 2 : Configuration du serveur OpenVPN
- Configuration du fichier server.conf
    avec ``sudo nano /etc/openvpn/server/server.conf``
    ![img](https://i.ibb.co/PvmLNwwQ/Capture-d-cran-2026-02-27-110235.png)

- Questions de configuration

    Que signifie dev tun ?

        ``dev tun`` (pour TUNnel) indique à OpenVPN de créer une interface réseau virtuelle de couche 3. 

    Différence entre UDP et TCP pour un VPN :

        - UDP (port 1194) est le protocole recommandé par défaut : il est beaucoup plus rapide car il n'attend pas d'accusé de réception pour chaque paquet envoyé.
        - TCP (port 443) est plus lent car il vérifie que chaque donnée est bien arrivée. On ne l'utilise généralement que comme solution de secours pour contourner des pare-feux restrictifs qui bloquent le trafic UDP, car il permet de faire passer le flux VPN pour du trafic web classique (HTTPS).

    Plage IP choisie : [10.8.0.0] car on choisit généralement une plage d'adresses privées distincte, telle que 10.8.0.0/24. Ce choix est motivé par la nécessité d'éviter les conflits d'adressage. Utiliser un sous-réseau en 10.x.x.x garantit que le trafic VPN est clairement identifié et routable sans ambiguïté.

- Routage et NAT

    Activation du forwarding IP :

        Fichier modifié : /etc/sysctl.conf
        Ligne décommentée : net.ipv4.ip_forward=1
        et on tape la commande **sudo sysctl -p** pour applique les changements

    ![img](https://i.ibb.co/gLVnKFpd/Capture-d-cran-2026-02-27-122040.png)

    Mettre en place une règle NAT pour avoir l'accès internet depuis le vpn
        
        mon interfaces reseaux est enp0s3 en regardant avec **ip a**
        et je tape cette commande **sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o enp0s3 -j MASQUERADE** elle permet à tes clients VPN de sortir sur Internet en utilisant l'adresse IP de ton serveur.
    ![img](https://i.ibb.co/67ffpVGn/Capture-d-cran-2026-02-27-122845.png)

- Démarrage et Analyse

    on demarre le service avec la commande suivante ``sudo systemctl start openvpn-server@server``
    et on verifie son status avec cette commande 
    ``sudo systemctl status openvpn-server@server``
    ![img](https://i.ibb.co/dZS5hdS/Capture-d-cran-2026-02-27-162039.png)

    Questions :
    Où se configure le paramètre ip_forward ?

        Le paramètre ip_forward se configure principalement dans le fichier /etc/sysctl.conf pour une modification permanente. Il faut décommenter ou ajouter la ligne net.ipv4.ip_forward=1. Pour une activation immédiate sans redémarrage, on peut utiliser la commande sysctl -w net.ipv4.ip_forward=1 ou écrire directement "1" dans le fichier virtuel /proc/sys/net/ipv4/ip_forward. C'est ce paramètre qui permet au serveur de se comporter comme un routeur entre les zones (WAN, LAN, DMZ).

    Quelle commande permet d'afficher les règles NAT actuelles ?

        Pour afficher les règles du NAT, on utilise iptables avec l'option spécifique à la table NAT avec cette commande "iptables -t nat -L -n -v"
    
    Pourquoi faut-il "masquerader" le réseau VPN ?

        Le "Masquerading" est une forme de NAT dynamique. Il est nécessaire pour que les clients VPN puissent accéder à Internet ou au réseau local de l'entreprise pour deux raisons l'acces a internet et le routage du retour  

## Partie 3 : Création du profil client
- Cree un fichier .ovpn

    

    Comment intégrer un certificat directement dans le fichier ?

        [Réponse : utilisation des balises <ca>, <cert>, <key>]

    Sécurité : Pourquoi la clé privée ne doit-elle jamais être partagée ?

        [Réponse]

- Tests et Validation

    Adresse IP obtenue sur le client : [192.168.1.40]

    Vérification du trafic :

        Commande utilisée : curl ifconfig.me ou traceroute
    ![img]()

    Blocage du port 1194 : Que se passe-t-il ?

        [Réponse]

## Partie 4 : Bonus (Optionnel)

Multi-clients :

Révocation :

Passage en TCP :

Double Authentification :
