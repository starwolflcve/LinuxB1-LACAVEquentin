# TP3
## I. Exploration en solo
### 1.Base64
- on crée un fichier `file_bin` avec cette commande `dd if=/dev/urandom of=file_bin bs=1k count=50`
![file_bin](https://i.ibb.co/jPrHW9bV/Screenshot-2026-02-15-13-00-57.jpg)
- on encoder le fichier en base64 avec `openssl base64 -e -in file_bin -out file_bin_b64`
- Vérifier que le fichier généré est bien un texte avec cette commande `cat file_bin_b64`
```
quand on tape cette commende on ouvre le fichier file_bin qui comptien une grande liste de caractere 
```
- on compare le taille du fichier en tapant cette commande `ls -l file_bin*`
![taille](https://i.ibb.co/NgNYtSg5/linux-2.jpg)
```
on remarque que le fichier base64 est plus lourd
```
- On décode le fichier base64 pour produire un nouveau fichier file_bin2 avec cette commande `openssl base64 -d -in file_bin_b64 -out file_bin2` et on regarde si ils sont identiques `diff -s file_bin file_bin2`
![linux3](https://i.ibb.co/RGC3snH7/linux3.jpg)
`les fichier sont identique`
### 2. AES (Chiffrement symétrique)
- On va générer un autre fichier texte message aléatoire qui va créer un fichier texte contenant tous les mots du dictionnaire ayant ker comme sous-chaîne avec cette commande `cat /usr/share/dict/words | grep ker  | tr "\n" " " >message`
![linux4](https://i.ibb.co/HfkcdTVJ/linux4.jpg)

- on va chiffrer le message en aes256 avec cette commande `openssl enc -e -salt -in message -out message_c -aes256 -pbkdf2 -md sha256`
![linux5](https://i.ibb.co/6c3j5YYk/linux5.jpg)

- on procède maintenant au déchiffrement avec cette commande `openssl enc -d -in message_c -aes256 -pbkdf2 -md sha256`
![linux6](https://i.ibb.co/n81TtyWh/linux6.jpg)

- on peux voir le cryptogramme obtenu (message_c) avec `cat message_c`
![linux7](https://i.ibb.co/60Wtdr1g/linux7.jpg)

- Pour chiffrer et encoder en même temps en base64 avec cette commende `openssl enc -e -a -salt -in message -out message_c2 -aes256 -pbkdf2 -md sha256`et on le ragarde avec cette commande `cat message_c2`
![linux8](https://i.ibb.co/cSVDMvsZ/linux8.jpg)

### 3. RSA (Chiffrement asymétrique)
- Générez une paire de clés RSA de 2048 bits qui se nomme cle_lacQ.pem avec cette commande `openssl genrsa -out cle_lacQ.pem 2048`et on la regarde avec `cat cle_lacQ.pem`

![linux9](https://i.ibb.co/d0xWVmD9/linux9.jpg)

- On va regarder un peu plus en détail les paramètres de la clé avec cette commande `openssl rsa -in cle_lacQ.pem -text -noout`
![linux](https://i.ibb.co/7dXjmN1S/Screenshot-2026-02-15-17-02-17.jpg)

- Maintenant on va protéger notre paire de clés RSA avec un chiffrement AES avec cette commande `https://i.ibb.co/vCRS5HRW/Screenshot-2026-02-15-17-13-55.png`
![linux](https://i.ibb.co/vCRS5HRW/Screenshot-2026-02-15-17-13-55.png)

- on va maintenant exporter la clé publique garce a cette commende `openssl rsa -in cle_lacQ.pem -pubout -out clepublique_lacQ.pem`et on verifie avec cette commande `cat clepublique_lacQ.pem`
![linux10](https://i.ibb.co/FbRzw5wn/Screenshot-2026-02-15-17-20-27.png)

- On peut visualiser les paramètres de la clé publique :
`openssl rsa -in clepublique_lacQ.pem -pubin -text -noout`
![linux11](https://i.ibb.co/ZRnwRDkc/Screenshot-2026-02-15-17-22-36.png)

- Que pouvons nous voir dans ces paramètres ?
```
on constate que d'apres ces parametres la cle publique, contrairement à la clé privée, la clé publique ne contient aucune information secrète
```
- On va maintenant créer une passphrase de votre choix avec vim avec `nano pass_lacQ`
- On chiffre le fichier avec la clé publique `openssl pkeyutl -encrypt -in pass_lacQ -inkey clepublique_lacQ.pem -pubin -out pass_lacQ_c` et on le fichier avec la clé avec `openssl pkeyutl -decrypt -in pass_lacQ_c -inkey cle_lacQ.pem`
![linux12](https://i.ibb.co/mFPW20rh/Screenshot-2026-02-15-17-43-24.png)
## II. Sans que je vous file les réponses à chaques étapes
### A. Base64
#### 1. Génération d’un fichier binaire

Créer un fichier data.bin contenant 100 Ko de données binaires aléatoires `dd if=/dev/urandom of=data.bin bs=1024 count=100` et vérifier sa taille `ls -lh data.bin`

![linux13](https://i.ibb.co/VcKCyk6P/Screenshot-2026-02-15-17-54-02.png)

#### 2. Encodage

Encoder le fichier en Base64 dans un fichier data.b64.`base64 data.bin > data.b64`
Afficher son contenu.`head -n 10 data.b64`
Comparer la taille de data.bin et data.b64.`ls -lh data.bin data.b64`
![linux14](https://i.ibb.co/Q7y3DzQr/Screenshot-2026-02-15-17-56-58.png)

#### 3. Décodage

Décoder le fichier data.b64 afin d’obtenir un fichier data_restored.bin.`base64 -d data.b64 > data_restored.bin`
Vérifier que data.bin et data_restored.bin sont strictement identiques quand je tappe la commande il n'affiche rien se qui signifie que les deux fichier sont identique.`diff data.bin data_restored.bin`

![linux15](https://i.ibb.co/DP4qcKjk/Screenshot-2026-02-15-18-03-04.png)

#### 4. Questions


Base64 est-il un chiffrement ? Pourquoi ?
```
Non. Ce n'est pas un chiffrement mais un encodage.
```
Pourquoi la taille du fichier change-t-elle après encodage ?
```
L'encodage Base64 utilise 6 bits pour représenter un caractère, alors qu'un octet classique en utilise 8. Pour coder 3 octets de données (24 bits), le Base64 doit utiliser 4 caractères (4 x 6 bits = 24 bits). On utilise donc plus de caractères pour stocker la même quantité d'information.
```
Quel est approximativement le pourcentage d’augmentation ?
```
L'augmentation est d'environ 33%.
```
Quelle méthode permet de vérifier rigoureusement que deux fichiers sont identiques ?
```
La méthode la plus rigoureuse est le hachage (checksum). On compare les empreintes numériques des deux fichiers. Si les empreintes sont identiques, les fichiers le sont aussi au bit près.
```
### B. Chiffrement symétrique – AES

#### 1. Création d’un message

Créer un fichier confidentiel.txt contenant votre nom, la date et 5 lignes minimum avec cette commende `nano confidentiel.txt`

#### 2. Chiffrement

Chiffrer le fichier avec :
- AES 256
- Un sel
- Une dérivation de clé robuste
- Un algorithme de hachage sécurisé
en utilisant cette commande `openssl enc -aes-256-cbc -salt -pbkdf2 -iter 100000 -md sha256 -in confidentiel.txt -out confidentiel.enc`

Vérifier que le fichier obtenu est bien binaire avec `file confidentiel.enc` et il repond Data 
![linux16](https://i.ibb.co/B2TNHNC7/Screenshot-2026-02-15-18-29-25.png)

#### 3. Déchiffrement

Déchiffrer le fichier vers :
confidentiel_dechiffre.txt avec cette commende
`openssl enc -aes-256-cbc -d -salt -pbkdf2 -iter 100000 -md sha256 -in confidentiel.enc -out confidentiel_dechiffre.txt`
Vérifier que le contenu correspond exactement à l’original avec cette commande `cat confidentiel_dechiffre.txt`.
![linux17](https://i.ibb.co/53dTyxz/Screenshot-2026-02-15-18-32-48.png)

#### 4. Analyse

Chiffrer une seconde fois le même fichier avec le même mot de passe.`openssl enc -aes-256-cbc -salt -pbkdf2 -iter 100000 -md sha256 -in confidentiel.txt -out confidentiel2.enc`
Comparer les deux fichiers chiffrés.`sha256sum confidentiel.enc confidentiel2.enc`

![linux18](https://i.ibb.co/dnQSL3j/Screenshot-2026-02-15-18-36-11.png)

#### 5. Questions

Pourquoi les deux fichiers chiffrés sont-ils différents ?
```
À cause du Sel (Salt). Même si le message et le mot de passe sont identiques, le sel est généré aléatoirement à chaque fois, ce qui produit un résultat chiffré unique.
```
Quel est le rôle du sel ?
```
Il empêche les attaques par "tables de correspondance" (Rainbow Tables). Sans sel, un même mot de passe donnerait toujours le même résultat chiffré, permettant à un pirate de deviner ton mot de passe s'il l'a déjà rencontré ailleurs.
```
Que se passe-t-il si une option change lors du déchiffrement ?
```
Le déchiffrement échouera ou produira des données corrompues ("Bad decrypt"). Pour réussir, il faut exactement le même algorithme, le même mode, et la même méthode de dérivation.
```
Pourquoi utilise-t-on PBKDF2 ?
```
C'est une méthode qui rend la transformation du mot de passe en clé volontairement lente et complexe. Cela protège contre les attaques par force brute (tester des millions de mots de passe par seconde devient impossible).
```
Quelle est la différence entre encodage et chiffrement ?
```
Encodage (ex: Base64) : Change le format des données pour le transport. Pas de secret, n'importe qui peut décoder.

Chiffrement (ex: AES) : Protège la confidentialité. Nécessite une clé secrète pour retrouver les données.
```
### C. Cryptographie asymétrique – RSA

#### 1. Génération de clés

- Générer une paire de clés RSA 2048 bits avec cette commande pour rsa_private.pem et rsa_public.pem
 `openssl genrsa -aes256 -out rsa_private.pem 2048`

- Protéger la clé privée par un chiffrement.`openssl rsa -in rsa_private.pem -pubout -out rsa_public.pem`

- Afficher les paramètres détaillés de la clé privée avec `openssl rsa -in rsa_private.pem -text -noout`
![linux](https://i.ibb.co/bMwFCkY4/Screenshot-2026-02-15-19-29-06.png)

- Afficher les paramètres de la clé publique avec `openssl rsa -in rsa_public.pem -pubin -text -noout`
![linux](https://i.ibb.co/XrFPCZNn/Screenshot-2026-02-15-19-29-49.png)

- Comparer les deux.
```
on remarque que la cle public contient beaucoup moins d'information que la cle privee.
```

#### 2. Chiffrement asymétrique

Créer un fichier secret.txt avec `echo "Ceci est un secret asymétrique" > secret.txt`

Chiffrer ce fichier avec la clé publique avec `openssl pkeyutl -encrypt -in secret.txt -pubin -inkey rsa_public.pem -out secret.enc`

Le fichier chiffré devra s’appeler : secret.enc

Déchiffrer ensuite avec la clé privée avec `openssl pkeyutl -decrypt -in secret.enc -inkey rsa_private.pem`

![img](https://i.ibb.co/dwCrxxL3/Screenshot-2026-02-15-19-40-08.png)

#### 3. Questions

Pourquoi la clé privée ne doit-elle jamais être partagée ?
```
Parce qu'elle est la seule à pouvoir déchiffrer les messages envoyés avec la clé publique et la seule à pouvoir créer une signature numérique en votre nom. Si quelqu'un la possède, il peut lire tous vos secrets et usurper votre identité.
```
Pourquoi RSA n’est-il pas adapté au chiffrement de gros fichiers ?
```
Lenteur : Les calculs mathématiques (exponentiations modulaires) sont extrêmement gourmands en CPU par rapport à l'AES.

Limite de taille : Mathématiquement, on ne peut pas chiffrer un bloc de données plus grand que la taille du modulo (ex: pour 2048 bits, on est limité à environ 245 octets de données réelles à cause du "padding").
```
Quelles différences observe-t-on entre les paramètres d’une clé publique et d’une clé privée ?
```
Clé Publique : Contient uniquement le Modulo (n) et l'Exposant public (e).

Clé Privée : Contient tout (le modulo, l'exposant privé d, les nombres premiers p et q, etc.). Elle contient les secrets nécessaires pour inverser le calcul de chiffrement.
```
Quel est le rôle du modulo dans RSA ?
```
C'est la pièce maîtresse du système. Il sert de "boîte" dans laquelle tous les calculs sont effectués. C'est sa difficulté à être factorisé qui garantit la sécurité du RSA.
```
Pourquoi utilise-t-on souvent RSA pour chiffrer une clé AES plutôt qu’un document entier ?
```
On profite de la vitesse de l'AES pour chiffrer le gros document, puis on utilise la praticité du RSA pour chiffrer uniquement la petite clé AES. C'est ainsi que fonctionnent HTTPS et vos emails sécurisés.
```
### D. Signature numérique

#### 1. Création et signature

Créer un fichier contrat.txt avec cette commande `echo "Je m'engage à valider ce TP avec brio." > contrat.txt`

Générer son empreinte (hash), signer le fichier avec votre clé privée, le fichier de signature devra s’appeler : contrat.sig avec cette commande `openssl dgst -sha256 -sign rsa_private.pem -out contrat.sig contrat.txt`

![img](https://i.ibb.co/vvfg2zrN/Screenshot-2026-02-15-19-56-09.png)

#### 2. Vérification

Vérifier la signature avec la clé publique avec `openssl dgst -sha256 -verify rsa_public.pem -signature contrat.sig contrat.txt` et il me sort `Verified OK`

Modifier légèrement le fichier contrat.txt avec `echo "Je m'engage à NE PAS valider ce TP." > contrat.txt`

Refaire la vérification en reutilisant cette commande `openssl dgst -sha256 -verify rsa_public.pem -signature contrat.sig contrat.txt` et il m'affiche 
```
Verification failure
4047C65B27720000:error:02000068:rsa routines:ossl_rsa_verify:bad signature:../crypto/rsa/rsa_sign.c:430:
4047C65B27720000:error:1C880004:Provider routines:rsa_verify:RSA lib:../providers/implementations/signature/rsa_sig.c:774:
```
![img](https://i.ibb.co/27D0DR5C/Screenshot-2026-02-15-20-00-57.png)

#### 3.Questions

Que se passe-t-il après modification du fichier ?
```
La vérification échoue (Verification Failure). Cela arrive parce que le hachage (l'empreinte) du nouveau fichier ne correspond plus du tout à celui qui a été scellé dans la signature. Même un seul caractère modifié change complètement le résultat du hash.
```
Pourquoi ? ``
Quel est le rôle du hachage dans le mécanisme de signature ?
```
On ne signe pas le fichier entier (ce serait trop lent et trop lourd), on signe uniquement son empreinte (hash). Le hachage garantit que si le document change, l'empreinte change aussi. La signature "verrouille" cette empreinte pour prouver qu'elle est authentique.
```
Quelle différence entre signature numérique et chiffrement ?
```
Chiffrement : On utilise la clé publique du destinataire pour cacher le message. Seul lui peut le lire. (Objectif : Confidentialité).

Signature : On utilise sa propre clé privée pour marquer le message. Tout le monde peut le lire, mais tout le monde peut vérifier que c'est bien vous qui l'avez écrit. (Objectif : Authenticité et Intégrité).
```

