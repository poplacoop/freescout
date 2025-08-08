# FREESCOUT Docker pour pop la coop
## Pré-requis
Nécessite les paquets docker et docker-compose
- Vérifier si docker et docker-compose sont installés
```
apt list docker docker-compose
```
si le nom des paquets et leurs version ne s’affichent pas avec la mention "[installed]" ou "[upgradabe]"comme suit :
```
docker-compose/oldoldstable,now X.X.X all [installed]
docker/oldoldstable,now X.X.X all [installed]
git/oldoldstable X.X.X [upgradable from: XXXX]
```
- Alors il est nécessaire d'installer docker et docker-compose (sinon passer à la suite)
```
sudo apt-get install docker docker-compose
```

## Installation
Vérifier que le répertoire /home/docker/docker_freescout existe, sinon le créer
```
mkdir /home/docker/docker_freescout
```

Puis lancer la commande de clonage git vers ce réperoire (Les accès au git sont bien sûr nécéssaire):
- En SSH
```
git clone git clone git@github.com:poplacoop/freescout.git /home/docker/docker_freescout
```
- En HTTPS
```
git clone git clone https://github.com/poplacoop/freescout.git /home/docker/docker_freescout
```

Puis on rentre dans le répertoire cloné
```
cd /home/docker/docker_freescout
```

Les dockers sont installés et prêt à être lancés.

## Configuration post lancement

Le fichier de lancement des docker contient un paramétrage suffisant et opérationnel.
Si vous souhaitez changer des paramètres avant de lancer le docker, il faut modifier le fichier
``
docker-compose.yml
``
présent à la racine de NOM_REPERTOIRE.
Les paramètres intéressants sont :
- Les numéros de port utilisés (pour ne pas empiéter sur les autres) ;
- Les différents volumes qui sont les fichiers partagés entre l'hôte et le docker
(/!\ Attention, le docker peut supprimer des fichiers sur la machine hôte) ;
- l'URL du site ;
- les différents nom de bases de données et identifiants de connexion.

## Lancement des dockers
Le lancement des dockers se fait depuis le répertoire /home/docker/docker_freescout avec la commande suivante :
```
docker-compose up -d
```
(Peut nécessité un sudo si vous ne faite pas parti du groupe docker).

Ou pour une version de docker-compose plus récente :
```
docker compose up -d
```
En cas de réussite, le message suivant s'affiche :
```
Creating network "docker_freescout_default" with the default driver
Creating freescout-db ... done
Creating freescout-app       ... done
Creating freescout-db-backup ... done
```

Il y a alors trois dockers qui tournent : un fresscout, un mariadb et un mariadb backup (on peut le vérifier avec la commande docker ps). Pas de conflit avec un mariadb local, les trois dockers communiquent uniquement entre eux.

## Reverse Proxy
Si le docker ne tourne pas en local, il faut le rendre visible de l'extérieur. Pour cela, il faut créer un nom avec le DNS associé à l'adresse URL paramétrée dans le docker compose.
S'il s'agit d'une adresse en HTTPS, il faut aussi configurer le protocole SSL avec certbot :
```
sudo certbot --nginx -d freescout.poplacoop.fr
```

## Mise à jour des dockers
Pour mettre à jour les dockers il faut se rendre dans le répertoire /home/docker/docker_freescout puis lancer trois commandes
Une commande de téléchargement des dernières images :
```
docker-compose pull
```

Puis une commande pour arrêter les dockers :
```
docker-compose down
```

Et enfin une commande pour les relancer en mode détaché:
```
docker-compose up -d
```


## Connexion aux dockers
Pour se connecter au dokers afin d'y effectuer des opérations de maintenances ou autres il faut commencer par afficher les dockers qui tournent :
```
docker ps
```
repérer le "CONTAINER ID" auquel on souhaite se connecter (``mariadb`` par exemple) puis lancer la commande de connexion :
```
docker exec -it "CONTAINER ID" bash
```
On est alors directement connecté sur le docker comme utilisateur root.

## Arrête des dockers
Pour arrêter les dockers il suffit de taper la commande depuis le répertoire /home/docker/docker_freescout:
```
docker-compose down
```
ou pour une version plus récente :
```
docker compose down
```
