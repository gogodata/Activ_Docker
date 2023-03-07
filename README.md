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
- Créer un compte Docker Hub si pas déjà fait

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


# Démarrage

Comme on est sur MAC, on utilise le runtimes docker Colima :  
`colima start`


Si besoin, on peut donner des paramètres de lancement pour augmenter les ressources allouées :  
`colima start --cpu 4 --memory 8 --disk 100`

