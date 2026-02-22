# TP5
## Partie 1
### 1. Accès à l’interface

Connectez-vous à l’interface web de pfSense.
Questions :


- Quelle est l’adresse IP du LAN  ?
`192.168.56.1`
- Quelle est l’adresse IP du WAN ?
`192.168.56.3`
- Pourquoi utilise-t-on HTTPS ?
`Pour chiffrer la communication entre le navigateur et pfSense, éviter que les identifiants et les données d’administration soient lisibles en clair sur le réseau.c'est ajouté de la sécurité`
- Pourquoi faut-il changer les identifiants par défaut sur un pare-feu ?
`Parce que les identifiants par défaut sont connus publiquement, donc un attaquant pourrait prendre le contrôle de l’équipement s’ils ne sont pas changés`

### 2. Sécurisation de l’accès administrateur

Modifiez les paramètres du compte administrateur.
Questions :

- Où se gèrent les utilisateurs ?
`Dans l’interface d’administration de pfSense, menu System → User Manager`
- Qu’est-ce qu’un mot de passe robuste ?
`C'est un mot de passe qui possede beaucoups de caractere qui mélangeant majuscules, minuscules, chiffres et caractères spéciaux, et qui n’est pas basé sur des informations personnelles`
- Pourquoi sécuriser en priorité l’accès admin sur un équipement réseau ?
`Parce que si quelqu’un obtient l’accès admin, il peut modifier toutes les règles de sécurité, ouvrir le réseau, espionner ou couper les services.`

## Partie 2 – Comprendre les interfaces réseau

### 3. Vérification des interfaces

Vérifiez l’affectation WAN / LAN.
Questions :

- Quelle interface permet l’accès Internet ?
`L’interface WAN, reliée au réseau externe`
- Quelle interface correspond au réseau interne ?
`L’interface LAN, reliée au réseau local`
- Que se passerait-il si les interfaces étaient inversées ?
`Le réseau interne serait exposé vers l’extérieur et les règles ne fonctionneraient plus comme prévu, ce qui pourrait soit couper l’accès Internet, soit ouvrir dangereusement le LAN vers l’extérieur`

## Partie 3 – Configuration des services réseau

### 4. DHCP

Configurez le serveur DHCP pour le réseau LAN.
Questions :

- Pourquoi utiliser DHCP plutôt qu’une IP fixe ?
`Pour attribuer automatiquement des adresses IP aux clients, éviter les erreurs de configuration manuelle et faciliter la gestion d’un grand nombre de machines.`
- Quelle plage d’adresses choisir ?
`Cela dépend de votre sous-réseau. Pour un LAN classique en 192.168.1.0/24, on choisit souvent une plage comme 192.168.1.100 à 192.168.1.200.`
- Quelles adresses faut-il éviter d’inclure dans la plage ?
`Il faut exclure l'adresse de l'interface LAN du pare-feu lui-même (la passerelle) et les adresses des équipements qui doivent rester fixes (serveurs, imprimantes, switches).`

Vérification :

Ubuntu obtient-elle automatiquement une adresse IP ?
``

### 5. DNS

Activez et configurez le résolveur DNS.
Questions :

Pourquoi un pare-feu peut-il jouer le rôle de serveur DNS ?

`En tant que passerelle par défaut, le pare-feu est le point de contact central. Il peut mettre en cache les requêtes pour accélérer la navigation et filtrer les domaines malveillants avant même que le trafic ne sorte du réseau.`

Que se passe-t-il si le DNS ne fonctionne pas mais que le ping vers 8.8.8.8 fonctionne ?

`Si le ping vers 8.8.8.8 (IP de Google) fonctionne mais pas le DNS, cela signifie que la connectivité IP est opérationnelle, mais que la résolution de noms est cassée. Vous pouvez joindre internet via des chiffres, mais pas via des noms (ex: google.fr).`

## Partie 4 – Autoriser l’accès Internet

### 6. Règles de pare-feu

Quelle doit être la source ?
``

Quelle doit être la destination ?
``

Faut-il autoriser tous les protocoles ?
``

- Tests :

Ping vers pfSense
![img]()

Ping vers 8.8.8.8
![img]()

Test DNS
![img]()

Accès web
![img]()

### 7. NAT

Vérifiez la configuration du NAT sortant.
``

Questions :

Pourquoi le NAT est-il nécessaire avec une interface WAN en NAT ?
`Les adresses IP privées (LAN) ne sont pas routables sur Internet. Le NAT remplace l'IP privée de votre PC par l'IP publique (ou WAN) du pare-feu pour que les paquets puissent voyager sur le web.`

Quelle est la différence entre NAT automatique et manuel ?
`L'automatique génère les règles dès qu'une interface est créée. Le manuel permet un contrôle précis (ex: rediriger seulement certains flux ou changer l'IP de sortie).`

Comment vérifier qu’une traduction d’adresse a lieu ?
`Allez dans Status > Filter Reload`

## Partie 5 – Filtrage

### 8. Blocage d’un site spécifique

Bloquez l’accès à un site web de votre choix.
``

Questions :


Faut-il bloquer par IP ou par nom de domaine ?
`Le blocage par domaine (via DNS ou proxy) est préférable car un site comme Facebook possède des milliers d'IP qui changent souvent.`

Que se passe-t-il si le site utilise HTTPS ?
`Le contenu est chiffré. Le pare-feu ne voit pas l'URL exacte (ex: /photos), il voit seulement le domaine via le certificat SSL ou la requête DNS.`

Pourquoi le blocage par IP peut-il être contourné ?
`Si vous bloquez monsite.com, un utilisateur averti peut parfois taper l'IP directement ou utiliser un VPN/Proxy.`

Testez et observez les logs.
### 9. Blocage d’une catégorie de sites (jeux d’argent)

Créez une solution propre et maintenable pour bloquer plusieurs sites.
tips : réfléchissez à l’intérêt des alias.
``

Questions :

Pourquoi ne pas créer une règle par site ?
`Pour la propreté. Au lieu de 50 règles, vous avez une seule règle qui pointe vers un Alias contenant 50 sites.`

Où se créent les alias ?
`Dans Firewall > Aliases.`

Comment vérifier qu’une règle bloque réellement le trafic ?
`Vérifiez le compteur de paquets à gauche de la règle dans l'interface pfSense.`

## Partie 6 – Aller plus loin (partie plus tendue)

### 10. Blocage par catégorie (réseaux sociaux)


Créez un alias pour une nouvelle catégorie.
Implémentez une règle.
Analysez les logs.

Question :

Que se passe-t-il si la règle est placée sous une règle "Pass Any" ?
`Elle ne sera jamais appliquée. pfSense lit les règles de haut en bas. Dès qu'une règle correspond (le "Pass Any"), il s'arrête là. Les blocages doivent toujours être en haut.`

### 11. Règles horaires

Créez un horaire.
Appliquez-le à une règle existante.

Questions :

Pourquoi les règles horaires sont-elles utiles en entreprise ?
`Limiter le surf récréatif pendant les heures de bureau, ou couper l'accès internet d'un atelier la nuit pour réduire la surface d'attaque.`

### 12. Serveur web local

Installez un serveur web sur Ubuntu.
Objectifs :

Autoriser un accès spécifique
Bloquer les autres

Questions :

Filtrer par IP source ?
``

Filtrer par port ?
``

Pourquoi le pare-feu protège-t-il le LAN même en réseau interne ?
``

### 13. Logs et analyse

Activez la journalisation sur certaines règles.
Questions :


Différence entre paquet bloqué et autorisé
Identifier quelle règle a déclenché le blocage
Comprendre le sens du trafic

### 14. DMZ

Mettez en place une zone DMZ.
Questions :


Qu'est ce qu'une DMZ ?
Pourquoi isoler un serveur ?
Une machine en DMZ peut-elle accéder au LAN ?
Le LAN peut-il accéder librement à la DMZ ?

### 15. Filtrage MAC

Testez le filtrage par adresse MAC.
Question :


Le filtrage MAC est-il réellement sécurisé ?
Pourquoi est-il facilement contournable ?

### 16. Portail captif

Implémentez un portail captif.
Questions :


Dans quels contextes utilise-t-on cela ?
Quelle(s) avantage(s) avec une simple règle de pare-feu ?

### 17. Sauvegarde / restauration


Sauvegardez la configuration.
Modifiez-la.
Restaurez-la.

Question :

Pourquoi la sauvegarde régulière est-elle essentielle en production ?
