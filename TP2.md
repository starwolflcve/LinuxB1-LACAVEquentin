# TP2 
Denael ROUDAUT CAMOUSSEIGHT, Tymotheusz Mlodovitch, quentin lacave

## I. Exploration locale en solo

### 1. Affichage d'informations (CLI)

#### Interface Wi-Fi
- **Nom de l'interface :** en0
- **Adresse MAC :** 9e:72:ed:1f:fb:d5
- **Adresse IP :** 192.168.1.246
- **Adresse de Réseau** : 192.168.1.0
- **Adresse de Broadcast** : 192.168.1.255

#### Interface Ethernet
- **Nom de l'interface :** eth0
- **Adresse MAC :** 
- **Adresse IP :** 
- **Adresse de Réseau :** 
- **Adresse de Broadcast :** 

#### Passerelle (Gateway)
- **Commande utilisée :** netstat
- **Adresse IP de la passerelle Wi-Fi :** 192.168.1.1

#### Affichage d'informations (GUI)

- **Méthode utilisée :** je suis allez dans Menu Pomme > Réglages Système > Réseau > Wi-Fi > Bouton "Détails" à côté du réseau connecté.
- **Informations Wi-Fi relevées :**
    - **IP :** 192.168.1.246
    - **MAC :** 14:7f:ce:93:27:a8
    - **Gateway :** 192.168.1.1

#### Questions
**À quoi sert la gateway dans le réseau d'Ingésup ?**

`La gateway est l'équipement qui sert de point de sortie au  segment de réseau local.`

### 2. Modifications des informations

#### A. Modification d'adresse IP - Partie 1

| Type d'adresse | Valeur calculée |
| :--- | :--- |
| **Première IP disponible** | 192.168.1.1 |
| **Dernière IP disponible** | 192.168.1.254 |

**Expérience de changement d'IP :**
- Nouvelle IP choisie : 192.168.1.11
- Résultat de la modification : SUCCÈS
```
j'ai pris une ip non utilise la 192.168.1.11. je suis allez dans mes reglage de mon wifi pour faire passer la configuration reseau de automatique a manuelle et j'ai changé IP en rentrant adresse ip (192.168.1.11). Tout fonctionne normalement car je peux naviger et ja'i pu ping google (8.8.8.8)
```

#### B. Analyse avec Nmap

- **Commande exécutée :** `nmap -sn 192.168.1.0/24`
- **Nombre d'hôtes détectés :** 4 (j'ai 4 ip qui sont prises)
- **Adresse IP libre identifiée :** 192.168.1.11

#### C. Modification d'adresse IP - Partie 2
- Modifiez de nouveau votre adresse IP vers une adresse IP que vous savez libre grâce à nmap
```
j'ai modifié mon adresse IP avec une adresse IP que je sais libres grace a `nmap`. j'ai pris 192.168.1.11 je suis retournée dans mes paramètres réseau et j'ai remplacé l'IP. Et le test fonctionne car j'ai pu ping google et j'ai pu naviguer sur le web 
```
---
## II. Exploration locale en duo




## III. Manipulations d'autres outils/protocoles côté client

### 1. DHCP

- adresse IP (DHCP) du réseau WiFi : 198.168.1.246
- date d'expiration de l'IP : 

- **fonctionnement DHCP**
```
 Le DHCP est un protocole de la couche Application. Le rôle de ce protocole de couche application est de configurer dynamiquement un réseau sur l’hôte. Il facilite l’attribution de l’adresse IP du réseau de groupe de travail. Par conséquent, il n’exige pas que l’administrateur adresse IP statique à l’hôte à l’avance. Par conséquent, il n’y a pas de risque que les équipements terminaux soient configurés à double adresse sur le réseau. b. Le Processus de Négociation (DORA) L’attribution de l’adresse IP a un processus de quatre phases basées sur les messages UDP avec Port 67 et Port 68.
```
- nouvelle adresse IP (en ligne de commande)
```

```

### 2.DNS

- **adresse IP du serveur DNS** :
- 