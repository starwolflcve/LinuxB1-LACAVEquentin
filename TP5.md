# TP5
## Partie 1
### 1. Accès à l’interface

Connectez-vous à l’interface web de pfSense.
Questions :


- Quelle est l’adresse IP du LAN  ?
`192.168.56.3/24`
- Quelle est l’adresse IP du WAN ?
``
- Pourquoi utilise-t-on HTTPS ?
``
- Pourquoi faut-il changer les identifiants par défaut sur un pare-feu ?
``

### 2. Sécurisation de l’accès administrateur

Modifiez les paramètres du compte administrateur.
Questions :


- Où se gèrent les utilisateurs ?
``
- Qu’est-ce qu’un mot de passe robuste ?
``
- Pourquoi sécuriser en priorité l’accès admin sur un équipement réseau ?
``

## Partie 2 – Comprendre les interfaces réseau

### 3. Vérification des interfaces

Vérifiez l’affectation WAN / LAN.
Questions :

- Quelle interface permet l’accès Internet ?

- Quelle interface correspond au réseau interne ?

- Que se passerait-il si les interfaces étaient inversées ?

## Partie 3 – Configuration des services réseau

### 4. DHCP

Configurez le serveur DHCP pour le réseau LAN.
Questions :


Pourquoi utiliser DHCP plutôt qu’une IP fixe ?
Quelle plage d’adresses choisir ?
Quelles adresses faut-il éviter d’inclure dans la plage ?

Vérification :


Ubuntu obtient-elle automatiquement une adresse IP ?

### 5. DNS

Activez et configurez le résolveur DNS.
Questions :


Pourquoi un pare-feu peut-il jouer le rôle de serveur DNS ?
Que se passe-t-il si le DNS ne fonctionne pas mais que le ping vers 8.8.8.8 fonctionne ?

## Partie 4 – Autoriser l’accès Internet

### 6. Règles de pare-feu

Configurez les règles nécessaires pour permettre aux machines du LAN d’accéder à Internet.
info :
pfSense applique les règles de haut en bas.
Questions :


Quelle doit être la source ?
Quelle doit être la destination ?
Faut-il autoriser tous les protocoles ?

Tests :


Ping vers pfSense
Ping vers 8.8.8.8
Test DNS
Accès web

Si ça ne fonctionne pas :


Où regarder ? (Logs ? NAT ? Règles ?)

### 7. NAT

Vérifiez la configuration du NAT sortant.
Questions :


Pourquoi le NAT est-il nécessaire avec une interface WAN en NAT ?
Quelle est la différence entre NAT automatique et manuel ?
Comment vérifier qu’une traduction d’adresse a lieu ?

## Partie 5 – Filtrage

### 8. Blocage d’un site spécifique

Bloquez l’accès à un site web de votre choix.
Questions :


Faut-il bloquer par IP ou par nom de domaine ?
Que se passe-t-il si le site utilise HTTPS ?
Pourquoi le blocage par IP peut-il être contourné ?

Testez et observez les logs.
### 9. Blocage d’une catégorie de sites (jeux d’argent)

Créez une solution propre et maintenable pour bloquer plusieurs sites.
tips : réfléchissez à l’intérêt des alias.
Questions :


Pourquoi ne pas créer une règle par site ?
Où se créent les alias ?
Comment vérifier qu’une règle bloque réellement le trafic ?

## Partie 6 – Aller plus loin (partie plus tendue)

### 10. Blocage par catégorie (réseaux sociaux)


Créez un alias pour une nouvelle catégorie.
Implémentez une règle.
Analysez les logs.

Question :

Que se passe-t-il si la règle est placée sous une règle "Pass Any" ?
### 11. Règles horaires


Créez un horaire.
Appliquez-le à une règle existante.

Questions :

Pourquoi les règles horaires sont-elles utiles en entreprise ?
### 12. Serveur web local

Installez un serveur web sur Ubuntu.
Objectifs :


Autoriser un accès spécifique
Bloquer les autres

Questions :


Filtrer par IP source ?
Filtrer par port ?
Pourquoi le pare-feu protège-t-il le LAN même en réseau interne ?

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
