# Activ_Docker

Matériel utilisé : MAC M1 Pro 16g

MacOS à date : Ventura 13.2

On se concentre donc ici sur l'utilisation de Docker sur MacOS.

Pour Windows, quelques adaptations sont à prévoir (WSL etc..)

# Prérequis

- Version MacOS à jour
- Homebrew (packages manager)
- Colima : suite à la mise à jour des accords de service le 31 août 2021, l'utilisation de Docker Desktop en milieu pro est payante, donc on choisit l'alternative open-source Colima. Colima est une VM qui fournit des runtimes docker sur MacOS et Linux.
- IDE (VS Code pour moi)
- Avoir/Créer un compte Docker Hub

# Documentation Docker

Pour avoir une perspective sur l'outil :
https://docs.docker.com/get-started/overview/

Manuels importants sur Docker :
- https://docs.docker.com/engine/
- https://docs.docker.com/build/
- https://docs.docker.com/compose/
- https://docs.docker.com/docker-hub/


Pour créer un projet à partir d'une image Python :
https://docs.docker.com/language/python/

Bonnes pratiques pour développer sur docker :
https://docs.docker.com/develop/

Différentes méthodes de configuration d'environnement (Dockerfile, Composefile, CLI etc..) :
https://docs.docker.com/reference/

En pratique, il est assez rare d'utiliser la CLI docker dans un projet.
On build nos images avec un Dockerfile et on paramètre nos containers avec Compose.  

## La principale différence entre Dockerfile et docker-compose
Le contenu d'un Dockerfile décrit comment créer et construire une image Docker, tandis que docker compose est une commande qui exécute des conteneurs Docker en fonction des paramètres décrits dans un fichier docker-compose.yaml.  

# Network 

Il y a plusieurs types de network sur Docker :
- bridge: c'est le network driver par défaut si on ne spécifie pas de driver lors de la création d'un network. Il est généralement utilisé lorsque notre application s'exécute dans des conteneurs standalone (autonomes) qui doivent communiquer entre eux.
- host: Pour des conteneurs standalone, le host network supprime l'isolation réseau entre le container et le host (la machine sur laquelle tourne notre Docker). Donc ce network utilise directement le réseau de l'host.
- overlay: Le network Overlay connecte plusieurs daemons Docker ensemble et permettent aux services Swarm de communiquer entre eux. Il permet aussi de faciliter la communication entre un service swarm et un conteneur standalone, ou entre deux conteneurs standalone sur différents daemons Docker. Cette stratégie supprime la nécessité d'effectuer un routage au niveau du système d'exploitation entre ces conteneurs.
- ipvlan: pour les utilisateurs intéressés, les réseaux IPvlan donnent aux utilisateurs un contrôle total sur l'adressage IPv4 et IPv6.
- macvlan: Les réseaux Macvlan vous permettent d'attribuer une adresse MAC à un conteneur, le faisant apparaître comme un périphérique physique sur votre réseau. Le daemon Docker achemine le trafic vers les conteneurs par leurs adresses MAC. L'utilisation du pilote macvlan est parfois le meilleur choix lorsqu'il s'agit d'applications "legacy" qui s'attendent à être directement connectées au réseau physique.
- none: Conteneur attaché à aucun réseau. Usually used in conjunction with a custom network driver. none is not available for swarm services. Le paramètre --network none lors du démarrage d'un conteneur permet de désactiver complètement la pile réseau pour n'avoir seulement que le loop device de créer. Le loop device est un périphérique bloc qui mappe ses blocs de données non pas sur un périphérique physique tel qu'un disque, mais sur les blocs d'un fichier normal dans un filesystem ou sur un autre block device.  
`docker run --rm -dit --network none --name no-net-alpine alpine:latest bash`
- Network plugins: On peut installer des third-party network plugins avec Docker. Ils sont disponibles sur Docker Hub ou directement  third-party vendors.

## Les intervalles de ports par défaut
On distingue trois intervalles de ports distincts définis par Internet Assigned Numbers Authority (IANA) :  
- Les ports de 0 à 1 023, ports réservés, sont essentiellement utilisés par des services et applications réseaux. Par exemple SSH, HTTP, SMTP etc.
- Les ports de 1 024 à 49 151, ports reconnus, sont souvent utilisés comme port local afin d’effectuer une connexion distante. En général, ces derniers sont entre 1024 et 5000.
- Les ports de 49 152 à 65 535 sont des ports dynamiques pour les requêtes TCP ou UDP

# Volume
Le principal problème d'un container Docker de base est que les données ne sont pas gardées sur disque une fois le container supprimé ou arrêté ?


# Démarrage

Comme on est sur MAC, on utilise le runtimes docker Colima :  
`colima start`. 

Si besoin, on peut donner des paramètres de lancement pour augmenter les ressources allouées :  
`colima start --cpu 4 --memory 8 --disk 100`.  



