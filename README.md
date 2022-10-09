# Jeu du chat bouilli
Le **chat bouilli** se joue avec un jeu de Tarot de 78 cartes. 
Ce jeu ressemble au solitaire traditionnel mais il a la particularité de se jouer à plusieurs.
Je ne sais pas exactement quelle est l'origine de ce jeu ou s'il existe sous un autre nom.
Ce projet est une application web permettant de jouer en multijoueurs en ligne au **chat bouilli**.
Techniquement, le code source est écrit en **JavaScript** pur aussi bien pour la partie client
que la partie serveur.

# Cahier des charges
* Application légère avec le moins de dépendances possible.
* Performances suffisantes pour du jeu en temps réel.
* Partie en cours stockée en RAM sur le serveur pas de sauvegarde persistante.
* Vérification des règles côté client et serveur avec du code partagé.
* Code modulaire et commenté pour faciliter la maintenabilité.
* Utilisation d'une base de données pour authentifier les joueurs et réaliser un classement.
* Interface graphique simple et intuitive de préférence sans utiliser de canvas et en limitant les animations.
* Comportement responsive et résolution adaptée aux ordinateurs et mobiles.
* Pas d'intelligence artificielle ni d'assistance pour les possibilités de mouvement.

# Règles du jeu
## Ordre des cartes
Les figures et les atouts sont indépendants.
* Figures : `Roi > Reine > Cavalier > Valet > 10 > 9 > 8 > 7 > 6 > 5 > 4 > 3 > 2 > As` 
* Atouts : `21 > 20 > ... > 1 > Excuse`

## Préparation
On désigne le premier joueur par exemple celui avec le moins de points de victoire.
Il distribue alors les cartes en partant de lui-même.

Les 78 cartes sont mélangées par effeuillage puis sont distribuées face cachée 
à chaque joueur dans le sens des aiguilles d'une montre de manière équitable. 
Au cours de la distribution 5 cartes devront être choisies aléatoirement et disposées
face cachée au centre de la table de jeu. Une fois la distribution finie ces 5 cartes
peuvent être retournées. Il s'agit des **colonnes**. Les joueurs ne doivent pas regarder 
les cartes qu'ils ont reçues.

## Déroulement
Le **chat bouilli** se joue au tour par tour. Le premier joueur retourne la première carte
de son **talon**. Plusieurs choix s'offrent alors à lui en fonction de sa carte :

* Si sa carte a une **valeur immédiatement inférieure** à l'une des 5 cartes des colonnes
et est d'une **couleur différente** (noir ou rouge) alors il peut poser sa carte sur le dessus
de la carte correspondante sur la table en la décalant légèrement vers le bas.
Dans le cas des atouts seule la condition de la valeur s'applique puisqu'ils n'ont pas de couleur.

* Si sa carte est un **As** ou l'**Excuse** alors il peut la poser au-dessus des 5 colonnes
dans des tas séparés. Il s'agit des tas finaux qu'on appelle les **piliers**. Une carte posée sur un
pilier doit avoir une **valeur immédiatement supérieure** à la dernière carte du pilier et être de **même couleur**
(sauf pour les atouts pour lesquels il n'y a pas de couleur).
Les cartes posées sur les piliers ne peuvent plus être retirées.

* Selon les mêmes conditions de placement sur les colonnes. Il peut poser sa carte sur la **pile de défausse**
d'un de ses adversaires.

* Sinon, il finit son tour en plaçant sa carte sur le **pile de défausse** à côté de son talon.

Tant que le joueur ne pose pas de carte dans sa propre pile de défausse, il continue à piocher dans son talon.
Lorsqu'il passe son tour c'est au joueur à sa gauche de jouer.

Lorsqu'un joueur arrive au bout de son talon il doit laisser sa pile de défausse visible jusqu'à ce que
ce soit de nouveau son tour. Il doit alors mélanger son talon comme suit :

* Retourner la pile de défausse pour qu'elle soit face cachée.

* Positionner le talon dans sa main gauche. 

* Prendre la première carte du talon dans sa main droite.

* Prendre les autres cartes du dessus du talon en les positionnant alternativement 
au-dessus et en-dessous du talon en formation dans sa main droite.

## La pile de défausse
La pile de défausse a un rôle important au **chat bouilli**. Elle peut être utilisée
pour ralentir un adversaire. Elle est parfois aussi utile pour poser plus rapidement une carte.
Lorsque c'est au tour d'un joueur en plus de poser la carte qu'il a pioché dans son talon il peut :

* Prendre la carte du dessus de sa pile de défausse pour la poser n'importe où 
(colonnes, piliers ou pile de défausse d'un adversaire) en respectant les conditions de placement.

* Prendre une colonne entière et la placer sur le pile de défausse d'un adversaire.

## L'ouverture
Lorsqu'on laisse vide une colonne on appelle cela une **ouverture**. Les ouvertures sont très utiles
pour placer des cartes de fortes valeurs comme le Roi. Il y a plusieurs manières de réaliser une ouverture :

* En déplaçant une colonne sur autre colonne.

* En déplaçant une colonne entière sur la pile de défausse d'un adversaire.

* En déplaçant toutes les cartes d'une colonne sur les piliers.

Lorsqu'un joueur crée une ouverture il doit **piocher une nouvelle carte** pour avoir 2 cartes en main,
sauf s'il choisit de poser sur l'ouverture le Roi qu'il a sur sa pile de défausse.

## Fin de partie
Pour gagner il faut se débarrasser de toutes ses cartes. La partie continue tant qu'il reste au moins
deux joueurs en lice. Plus les joueurs finissent tôt plus ils gagnent de points de victoire.

Le nombre de points accordé sera `nombre_points = nombre_joueurs - classement`
avec `classement` entre `[1;nombre_joueurs]`.

## Nombre de joueurs
Plus le nombre de joueurs est important et moins il y a de cartes dans chaque talon.

* 1 joueur : `78 - 5 = 73`
* 2 joueurs : `(78 - 5) / 2 = 36.5`
* 3 joueurs : `(78 - 5) / 3 = 24.3`
* 4 joueurs : `(78 - 5) / 4 = 18.25`
* 5 joueurs : `(78 - 5) / 5 = 14.6`
* 6 joueurs : `(78 - 5) / 6 = 12.2`
* 7 joueurs : `(78 - 5) / 7 = 10.4`
* 8 joueurs : `(78 - 5) / 8 = 9.1`
* 9 joueurs : `(78 - 5) / 9 = 8.1`
* 10 joueurs : `(78 - 5) / 10 = 7.3`

Théoriquement on pourrait aller jusqu'à 73 joueurs mais ce ne serait pas intéressant.
On peut fixer une limite maximale à 10 joueurs. Mais pour qu'une partie soit réellement
intéressante 4 joueurs paraît déjà beaucoup.
On remarque aussi qu'il y a toujours une injustice sur le nombre de cartes distribuées
du fait que 73 n'est pas divisible en entier entre `[2;10]` joueurs.

# Mise en production
## Installation de nodejs
Il ne faut pas installer nodejs avec `sudo apt install nodejs` car la version du paquet Linux est obsolète.
Il vaut mieux télécharger la dernière version LTS de nodejs depuis le site officiel.
L'inconvénient c'est qu'il faudra mettre à jour manuellement nodejs.
```shell
wget https://nodejs.org/dist/v16.17.1/node-v16.17.1-linux-x64.tar.xz
sudo tar -xf node-v16.17.1-linux-x64.tar.xz -C /opt
rm node-v16.17.1-linux-x64.tar.xz
echo 'export PATH=/opt/node-v16.17.1-linux-x64/bin/:$PATH' >> ~/.bashrc
node -v
```

## Configuration de nginx
À compléter

## Lancement du serveur
Pour lancer le serveur à chaque démarrage de l'ordinateur on utilisera **crontab**.
À compléter
