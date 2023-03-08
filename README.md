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

# Dockerfile

# Docker compose

## La principale différence entre Dockerfile et docker-compose
Le contenu d'un Dockerfile décrit comment créer et construire une image Docker, tandis que docker compose est une commande qui exécute des conteneurs Docker en fonction des paramètres décrits dans un fichier docker-compose.yaml.  
Un fichier docker-compose.yaml peut référencer un Dockerfile, mais un Dockerfile ne peut pas référencer un fichier docker-compose.  

# Network 

## Types de network sur Docker
- bridge: c'est le network driver par défaut si on ne spécifie pas de driver lors de la création d'un network. Il est généralement utilisé lorsque notre application s'exécute dans des conteneurs standalone (autonomes) qui doivent communiquer entre eux. Le network bridge par défaut est considéré comme un détail "legacy" de Docker et n'est pas recommandé pour une utilisation en production.
- host: Pour des conteneurs standalone, le host network supprime l'isolation réseau entre le container et le host (la machine sur laquelle tourne notre Docker). Donc ce network utilise directement le réseau de l'host.
- overlay: Le network Overlay connecte plusieurs daemons Docker ensemble et permettent aux services Swarm de communiquer entre eux. Il permet aussi de faciliter la communication entre un service swarm et un conteneur standalone, ou entre deux conteneurs standalone sur différents daemons Docker. Cette stratégie supprime la nécessité d'effectuer un routage au niveau du système d'exploitation entre ces conteneurs.
- ipvlan: pour les utilisateurs intéressés, les réseaux IPvlan donnent aux utilisateurs un contrôle total sur l'adressage IPv4 et IPv6.
- macvlan: Les réseaux Macvlan vous permettent d'attribuer une adresse MAC à un conteneur, le faisant apparaître comme un périphérique physique sur votre réseau. Le daemon Docker achemine le trafic vers les conteneurs par leurs adresses MAC. L'utilisation du pilote macvlan est parfois le meilleur choix lorsqu'il s'agit d'applications "legacy" qui s'attendent à être directement connectées au réseau physique.
- none: Ce network permet de désactiver toute la pile réseau d'un conteneur. Il est généralement utilisé en conjonction avec un custom network driver. none n'est pas disponible pour les services Swarm.
- Usually used in conjunction with a custom network driver. none is not available for swarm services. Le paramètre --network none lors du démarrage d'un conteneur permet de désactiver complètement la pile réseau pour n'avoir seulement que le loop device de créer. Le loop device est un périphérique bloc qui mappe ses blocs de données non pas sur un périphérique physique tel qu'un disque, mais sur les blocs d'un fichier normal dans un filesystem ou sur un autre block device. Pour créer un container sans réseau :  
`docker run --rm -dit --network none --name no-net-alpine alpine:latest bash`
- Network plugins: On peut installer des third-party network plugins avec Docker. Ils sont disponibles sur Docker Hub ou directement chez les third-party vendors. Les plugins network permettent d'étendre les déploiements pour prendre en charge un large éventail de technologies de mise en réseau, telles que VXLAN, IPVLAN, MACVLAN ou autres.

## Bonnes pratiques Network en production

Pour déployer une application en production à l'aide de Docker, il faut faire attention à la configuration réseau. Voici quelques bonnes pratiques à suivre  en environnement de production :
- Utilisez des réseaux Docker séparés pour les différents composants de votre application : il est recommandé de créer un réseau Docker pour chaque composant de votre application (par exemple, un réseau pour la base de données, un réseau pour l'API, etc.) afin d'isoler les différents composants et d'améliorer la sécurité.
- Utilisez des ponts réseau personnalisés plutôt que le réseau par défaut : il est recommandé de définir des paramètres de réseau personnalisés tels que des sous-réseaux, des adresses IP statiques, des noms de domaine personnalisés, etc.
- Utilisez un réseau externe pour exposer votre application : si besoin d'exposer l'application à l'extérieur de Docker (par exemple, pour accéder à une application Web depuis un navigateur), on peut créer un réseau externe Docker. Cela permet d'exposer les ports des conteneur sur l'host et de les rendre accessibles à l'extérieur.
- Utilisez un service de découverte de services : pour améliorer la gestion des réseaux et la résilience de votre application, il est possible d'utiliser un service de découverte de services tel que Consul, etcd ou Zookeeper. Ces services permettent aux différents composants de découvrir automatiquement les autres composants et de s'adapter aux changements de configuration en temps réel.
- Utilisez des équilibreurs de charge (load balancer) : si l'application nécessite de traiter de grandes quantités de trafic, vous pouvez utiliser des équilibreurs de charge tels que HAProxy ou NGINX pour répartir la charge entre les différents conteneurs Docker de l'application.

## Exposer les ports

Pour déployer une application, on doit configurer les ports exposés par chaque conteneur en fonction des besoins spécifiques de l'application. Voici quelques éléments à prendre en compte pour choisir la configuration des ports :

- Déterminer quels ports sont nécessaires pour chaque service : chaque service ou application déployé dans Docker peut avoir besoin de ports spécifiques pour communiquer avec d'autres services ou pour être accessible depuis l'extérieur. Par exemple, une application Web peut avoir besoin d'exposer le port 80 pour le trafic HTTP et le port 443 pour le trafic HTTPS.

- Éviter d'exposer des ports inutilement : il est important de minimiser l'exposition de ports non nécessaires pour réduire la surface d'attaque de l'applicationn notamment les ports SSH ou Telnet.

- Éviter d'utiliser des ports réservés : certains ports sont réservés par le système d'exploitation ou par des applications populaires, tels que les ports 80 et 443 pour HTTP et HTTPS. Évitez d'utiliser ces ports pour éviter les conflits. Pour rappel, on distingue trois intervalles de ports distincts définis par Internet Assigned Numbers Authority (IANA) :  
  - Les ports de 0 à 1 023, ports réservés, sont essentiellement utilisés par des services et applications réseaux. Par exemple SSH, HTTP, SMTP etc.
  - Les ports de 1 024 à 49 151, ports reconnus, sont souvent utilisés comme port local afin d’effectuer une connexion distante. En général, ces derniers sont entre 1024 et 5000.
  - Les ports de 49 152 à 65 535 sont des ports dynamiques pour les requêtes TCP ou UDP. 

- Utiliser des ports de conteneur aléatoires : on peut utiliser l'option -P pour mapper automatiquement les ports du conteneur à des ports aléatoires sur l'host Docker. C'est utile pour les conteneurs qui ont besoin de ports différents chaque fois qu'ils sont exécutés.

- Utiliser des ports de conteneur définis manuellement : pour les conteneurs qui nécessitent des ports spécifiques pour communiquer avec d'autres services ou pour être accessibles depuis l'extérieur, vous pouvez utiliser l'option -p pour mapper des ports spécifiques du conteneur à des ports spécifiques sur l'host Docker. C'est utile pour les conteneurs qui ont besoin de ports spécifiques chaque fois qu'ils sont exécutés.

## Pourquoi exposer les ports

Le port mapping rend les processus à l'intérieur du conteneur disponibles depuis l'extérieur. Lors de l'exécution d'un nouveau conteneur Docker, nous pouvons attribuer le mappage de port dans la commande docker run à l'aide de l'option -p :  
`docker run -d -p 81:80 --name httpd-container httpd`


La commande ci-dessus lance un conteneur httpd et mappe le port 81 de l'host au port 80 du conteneur. Par défaut, le serveur httpd écoute sur le port 80. Ainsi, nous pouvons maintenant accéder à l'application en utilisant le port 81 de la machine hôte :  
`curl http://localhost:81`


## Oublier les IP pour les DNS

L'utilisation d'IP statiques et d'IP pour commmuniquer avec des containers est à proscrire, c'est un anti-pattern qu'il faut éviter.  
Le daemon Docker dispose d'un serveur DNS built-in utilisé par défaut par les containers. On utilisera donc la méthode de DNS naming pour la communication entre nos conteneurs.  
Docker définit par défaut le nom d'hôte sur le nom du conteneur, on peut aussi définir des alias. Les alias sont indispensables dans le cas où on a besoin d'une applications identiques sur plusieurs containers différents.

On crée notre network :  
`docker network create toto_network`

On joue 2 fois la commande suivante pour créer 2 containers avec le meme network et alias :  
`docker container run -dit --network toto_network --network-alias toto_alias <IMAGE_NAME>`

<img width="1560" alt="image" src="https://user-images.githubusercontent.com/45535819/223751426-ea1a30b9-fc8d-4534-85d4-7797656920bd.png">

On voit bien les containers en listant :  
![image](https://user-images.githubusercontent.com/45535819/223752203-6c722cca-cb20-455f-b1e2-e56e0445d3da.png)

On pull l'image elasticsearch :  
`docker pull elasticsearch:8.6.2`

puis on run :  
`docker run -d --name elasticsearch --network toto_network --network-alias toto_alias -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag`


A la différence des IP, les hostnames et containers names ne changent pas donc cette résolution par DNS rend la communication beaucoup plus simple, surtout avec docker compose.

## Commandes courantes Network
- `docker network ls`
- `docker network inspect <NETWORK_NAME>`
- `docker network create <NETWORK_NAME>`
- `docker container run -d -p <PORT:PORT> --name <CONTAINER_NAME> --network <NETWORK_NAME> <IMAGE>`


# Volume
Le principal problème d'un container Docker de base est que les données ne sont pas gardées sur disque une fois le container supprimé ou arrêté ?


# Démarrage

Comme on est sur MAC, on utilise le runtimes docker Colima :  
`colima start`. 

Si besoin, on peut donner des paramètres de lancement pour augmenter les ressources allouées :  
`colima start --cpu 4 --memory 8 --disk 100`.  



