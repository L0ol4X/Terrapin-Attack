# TP: Attaque Terrapin

Comme vu lors de la présentation en TD, Terrapin est une attaque se basant sur SSH, une configuration en Man in The Middle et une troncature des messages contenant les informations importantes afin d'abaisser la sécurité globale des échanges.
Dans le cadre de ce TP vous allez devoir dans un premier temps réaliser l'attaque dans différents terminaux puis une interprétation des logs dans wireshark vous sera demandée.

Avant de commencer, clonez le dépôt sur la machine debian-1 du réseau secure de vdn: (1 point)
```
git clone https://github.com/L0ol4X/Terrapin-Attack.git
```


## Exercice 1 (7 points)
 


### Lancement des conteneurs :

Dans l'environnement VDN, sur la machine virtuelle debian-1 du réseau secure lancez la commande suivante : 

```bash
docker-compose up # Lancement des conteneurs
```

Dans un nouveau terminal, lancez la commande suivante afin de vérifier l'état des trois conteneurs et de récupérer leur nom:
```bash
docker-compose ps # Affichage des différents conteneurs
```

Les commandes suivantes sont à effectuer dans ce même deuxième terminal et vous permettront de récupérer les adresses IP de nos différents acteurs (le serveur et l'attaquant) :

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' terrapin-attack_serveur_1 # Affiche l'IP du serveur

docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' terrapin-attack_attaquant_1 # Affiche l'IP de l'attaquant
```


### Lancement des deux terminaux de nos acteurs 

Chaque terminal représentera un acteur. Il faut donc lancer ces commandes dans des terminaux différents : 


Pour le terminal attaquant, on peut réutiliser le second terminal précédemment ouvert et y lancer ceci :

```bash 
docker attach terrapin-attack_attaquant_1 # Pour le terminal de l'attaquant

python3 ext-downgrade/ext_downgrade_chacha20_poly1305.py --proxy-ip {ip_attaquant} --server-ip {ip_serveur}
```

Dans un nouveau terminal, lancez les commandes qui permettront de configurer le terminal victime : 

```bash
docker attach terrapin-attack_victime_1 # Pour le terminal de la victime

PATH="$PATH:/install/bin/" 

ssh victim@{ip_server} -vvv -o Ciphers=chacha20-poly1305@openssh.com
```

1) Vérifiez dans les logs que le serveur envoie bien le fichier EXT_INFO et que le client le reçoit bien (2 points)


Nous allons désormais réaliser l'attaque, quittez le précédent terminal connecté au serveur puis entrez cette commande dans le terminal victime : 
```bash
ssh victim@{ip_attaquant} -vvv -o Ciphers=chacha20-poly1305@openssh.com
```
Le mot de passe à entrer (quand demandé) est 'secret'.

2) Vérifiez que, cette fois-ci, notre fichier EXT_INFO n'est pas arrivé chez notre client. En revanche, quel message a-t-il reçu et quelles sont les lignes où l'on peut observer ceci ? (2 points)


3) Qui sont visiblement les acteurs de cet échange ?  (3 points)


## Exercice 2 (12 points)

La suite de ces questions se déroulent dans Wireshark, lancez l'application et désarchiver le fichier 'trame_ssh_attaque.tar.gz' pour ouvrir le fichier qu'il contient dans Wireshark.
```bash
tar -xf filename.tar.gz
```
1) Par quel biais l'attaquant altère ssh ? (2 points)

2) Vérifier les numéros de séquence. Que pouvez-vous remarquer ? (Via fichier WS) (2 points)

3) Quel protocole d'échange de clé a été utilisé ? Est-ce que ce protocole est inflencé par le MiTM ou n'est-il pas impacté par cette configuration ? (2 points)


4) Quels sont les algorithmes disponibles sur le serveur qui ont été tronqués ? (3 points)


Ouvrez le fichier 'trame_ssh_classic.pcapng' dans un nouveau Wireshark. Ce fichier contient les trames d'un ordinateur classique sur lequelle une connexion ssh a eu lieu. 

5) Grâce aux filtres, comparée les trames entre une connexion ssh normale et celle que vous avez obtenues pour l'attaque. Quel paquet envoyé par le serveur a permis l'attaque? (3 points)



