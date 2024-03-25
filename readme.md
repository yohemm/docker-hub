

# Linter
## Se reisengner sur hadolint


# commands :
- ip netns : network namespace
  - ls : listes les namespaces réseau
  - add \<namespace-name> : ajouté un name space réseau
  - exec \<namespace-name> \<cmd> : éxécuter un commande sur un nameSpace
- docket \<data> inspect : info
- docket manifest inspect <image> : toutes les infos de l'images
- docker ps : voir les conteners actifs (/!\ un container non-actifs peut l'être car n'exécutant aucune tache ex: image debian créant uniquement des users)
  - -a : voir tous les conteners
- docker run \<image> : créer un container
  - -v \<target:destination>
  - -u \<user>
  - -d : mode detach
  - --rm \<image> <cmd> : surcharger le processus principal 
  - --expose \<port> : mode detach
  - --hostname \<name> : variable d'env de l'hostname
  - --env \<var>="\<value>" : changer des variable d'env
  - --env-file \<file> : charge les variables d'env d'un fichier (une d'éclaration par ligne) 
  - --network \<network-name> : relie le container au réseau
  - -__p__ \<hostPort>:\<containerPort> : pusblish un port exposed sur l'host
  - -__P__ : pusblish automatiquement un port exposed sur l'host sur un IP supérieur à 32760
  - sleep infinity : force le container à être actif
  -  --name \<name>: définir un nom
  -  --mount type=: définir un nom
     -  volume,
        -  target=/path/
     -  tmpfs,
        -  destination=/path/
     -  bind,
        -  source=/path/,
        -  target=/path/
     - ,\[readonly|readwrite]
- docker stop
- docker login <registery> : connection a un registery
- docker logout <registery> : deconnection a un registery
- docker kill <container> : kill le processus main du ccontainer
- docker diff <container> : voir les différence depuis le début de l'executiuon
- docker images
  - inspect \<image-name:version> : donne le détail de l'image
- docker network
  - ls: lister les network
- docker volume
  - create 
    - \<name> : cré un volume
  - ls
- docker inspect \<name|containerID>
  - --format "{{.Mounts}}"
- docker start
- docker rm
  - -f : stop and supr
  - -f : stop and supr
  - { docker -q } : suprime tous conteneurs actif  
  - { docker -aq } : suprime tous conteneurs
- exec name process(ex:bash)
  - -ti : ouvre un programe
  - -u <\[UID|name]>: préciser un UID

- --volumes 
  - \<path> : crée un bind
  - \<nom> : crée un volume
- useradd \<name> : cré un user
  - -u <\[UID|name]>: préciser un UID

- -- mount \<type[bind/volumr/tmpfs]> : creation d'un volume
- docker network
  - ls : list
  - inspect \<NetworkID>
  - create \<name>
    - --driver=\<driver>
    - --subnet=\<ip/mask>
    - -o \<option> (ex: com.docker.network.bridge.name="newName") : ajouter une option
  - rm
  -  \[connect|disconnect] \<network> \<container> : rattacher un réseau à un container actif pour 
- docker history \<image> : détaille des layers d'une image
- docker build \<target> : build une image
  - -t \<name:version> : définir un nom d'image
  - -f \<Path/To/DockerFile> : définir fichier
  -  --target <arg> : changé un argument

# DockerFile
    Permet de créer sa propre image de container à partir d'un image source affin de preparer le deploiment du container et les processus a exécuter au sein du container.
```docker
INSTRUCTION agrument
```
- les intructions doivent être en MAJ
- les arguments en minuscule séparer par des espaces
- fichier plat, sans indentation/tabulation
## Astuce
Commentaire : #
caractére ':' dans des copy '@' car ':' est séparateeur
variable d'environement : ${'VAR_NAME'}
## MULTISRAGE
  Utilise une images à partir des différentes images déjà builder
  Builder à partir de plusieur FROM
Usage:
- taille des images
- Sécurité
- Conditions

## DockerIgnore
    Fichier de filtration pour l'importation de certains fichiers à l'image

- marche comme le .gitignore
- conseiller de mettre le Dockerfile
## conditionelle
définir le types de dévelopement vidia des ARG

pour en créer autant d'images qu'il y a de types de dévelopement et selectioner la principale
```docker
ARG TYPE = devopment

FROM a:v1.0.0 AS BASE
# ...

FROM base AS branch-version-development
# ...
ENV ENV=development
FROM base AS branch-version-production
# ...
ENV ENV=production
FROM branch-version-$[TYPE] AS final
# ...
```

- possibilité de débugé chaque stage en changant la parti env
    ```sh
    docker build -t app:1.0.0-debug --target base .
    docker run -it app:1.0.0-debug
    ```

### cas d'usage
#### 1. selection
```docker
FROM a:v3 AS a
RUN echo "test" > /var/test.1
# ... téléchargement d"outils dév 
# ... t"éléchagement de librairy 
FROM a:v3 AS b
RUN echo "test2" > /var/test.2
# ... téléchargement de language comilé
#   ... compilation de programe binaire
COPY --from=[0|a] /var/xavki.1 /
FROM a:v3
RUN echo "test2" > /var/test.3
COPY --from=[1|b] /var/xavki.2 /
# ... importation de librairy
# ... importaion de programe binaire

# seul  xavki.2 et xavki.3 reste
```
selection des fichiers de build à choisir afin d'optimiser de la place
#### 1. importation
```docker
FROM a:v3 AS a
RUN echo "test" > /var/test.1
COPY --from=[nginx:v1.2] /ect/nginx.conf /ect

# selection depuis l'extérieur
```
importation des fichiers de build à choisir afin d'optimiser de la place
## PARSER
### Syntaxe
```docker
# syntax=docker/dockerfile:1
```
viens du buildKit

Permet de définir la version du DockerFile
### Escape
```docker
# escape=\
```

Permet de définir un caractér affin d'ignorer un saut de ligne
## FROM
```docker
FROM [image:latest]
```
Permet de definir une image source

généralement utilisé en premier

__/!\\__ le choix de l'image source est trés important affin de simplifer le DockerFile
## VOLUME
```docker
VOLUME ["/path",]
```
- sous forme de liste
- déclare les volumes de manière séparés
## EXPOSE
```docker
EXPOSE [PORT]/[tcp|udp]
```
- plusieurs lignes possibles
- déclare des ports en écoute par le processus

## LABEL
```docker
LABEL [key=value]
```
- ajoute des metadatas pour l'image
## ARG
```docker
ARG [key=value]
```
- définit les variable de Build


Peut être définit avant l'instruction FROM
## ENV
```docker
ENV [key=value]
```
- définit les variable utlisable aussi bien par le build que par le container
## ADD
```docker
ADD [src] [sdt]
```
- ajoute des fichiers ou répertoirs
- peut être des URL
- contient de nombreuse option additionelle
## COPY
```docker
COPY [src] [sdt]
```
- ajoute des fichiers ou répertoirs
- contient de nombreuse option additionelle

__/!\\__ COPY est a privilégier par rapport à ADD pour des questions de sécurité
__/!\\__ COPY . . est à évité sans .dockerignore

## WORKDIR
```docker
WORKDIR [path]
```
- Répertoire de travaille dans le cadre du Build

- Possibilté d'en accumulé plusieurs workdir sur un seul DockerFile

__/!\\__ Les intructions suivantes (COPY, RUN, ...) le workdir seront utilisé dans le workdir

Lorsqu'un user se connect il arrive directement sur un workdir
## RUN
```docker
RUN [comand]
```
- executé une commande shell pour la preparation de l'image
- contient de nombreuse option additionelle

__/!\\__ trés utile mais faire atention à son utilisation pour les layers

__/!\\__ évité les layers
```docker
RUN ./code.sh # contient toutes les commandes
```

__/!\\__ tous installations (apt, wget, pip,...) dévra être versioner pour un meilleur maintenabilité

__/!\\__ uliser '--no-cache' pour les applications si il existe dans l'installer 
## CMD
    à pour objectif de définir les arguments du processus principal
```docker
CMD [list]
CMD ["python3", "-m", "flask", "run"]
CMD python3 -m flask run
```
- Définit le processus principal du container
- Commande lancée dans le container

__/!\\__ définir un user avant tous CMD (sécurité)

__/!\\__ Peut être surcharhée ! Ex : docker run --rm image:v.1.0.0 ping google.fr
### cas d'utilisation
1. freedom
    ```docker
    CMD ["sleep", "infinty", "flask", "run"]
    ```
2. Change Agrs
    ```docker
    ENTRYPOINT ["python3"]
    CMD ["-m", "flask", "run"]
    ```
3. Locker
    ```docker
    ENTRYPOINT ["python3", "-m", "flask", "run"]
    ```
## ENTRYPOINT
    à pour objectif de définir la commandes du processus principal
```docker
ENTRYPOINT [list]
ENTRYPOINT ["-m", "flask", "run"] # combinaison avec entrypoint
ENTRYPOINT ["python3", "-m", "flask", "run"]
ENTRYPOINT  python3 -m flask run
```
- Commandes lancé dans le container
- interaction avec CMD
- ne peut pas être surchargé

__/!\\__ définir un user avant tous ENTRYPOINT (sécurité)
# Images
    Une image est un éxecutable permetant de créer de container, il peut être typé OS ou Applicative

## FROM SCRATCH
# Containers
Instance d'une image

__/!\\__ les IPs des container ne sont pas static

    Exemple:
        start C1 (172.17.0.2)
        stop C1
        start C2 (172.17.0.2)
        start C1 (172.17.0.3)
# Volumes

__bind__ : ecrase les donné existante

__volume__ : partage/conserve les données de la sources avec la destination

__tmpfs__ : stocker en ram, toute Maj seras effacer à au stop du conteneur
# Layers
    Les layers sont des images aidant à créer l'image principal
    Stockage partager entre image affin d'optimisé la taille de chaqu'une des images
- optimisation du cache
- Si layers déja présent pour une autre image alors on ne la retelecharge pas (docker compare les hashs des layers)
- Utilisation du COW (Copy On Write)
- chaque instructions(RUN) pour créer l'image est associer à un layer

1. tous les layers créer sont en readOnly
2. Le layer Main (des container uniquement) est en write

__/!\\__ Toute chose confidencielle sur un layer doit être suprimer avant le suivant
## Bonnes utilisation
__/!\\__ Une mauvaise utilisation peut conduire à de gros probleme e sécurité

- les images doivent contenir des instructions regroupper entre elle (ex: apt update && apt install)
- fractionner/ordonner les layers affin de bénéfisier du plein potentiel du cache
  - mieux faut une grosse hiérachi
  - les instructions variable ou complexe en fin de ligne
- pas de Secret

```docker
# Seulement 3 layers sont utiliser (les 3 RUN plus la )

FROM docker.io/debian:lasted
RUN apt update -qq && apt install -qq -y wget && apt clean && rm -rf /var/lib/apt/lists/
RUN wget http://.../10B.zip && rm -f 10MB.zip
```

# Network
__/!\\__ il ne faut pas se fier au IP car variable mais plutot au network-name

## Créer un réseau complexe
### 1. Créer des namespace
```
ip netns add <namespace-name>
```
### 2. Creation des vethernet (cables) & interfaces

```
ip link add <eth> type veth peer name <peer>
```
- \<eth> : nom du lien/cable
- \<peer> :  interface du container

### 3. Ajout des interfaces au namespace
```sh
ip link set <peer> netns <namespace-name>
```
- \<namespace-name> : nom du namespace
- \<peer> :  interface à relier au namespace

### 3. Activation des vethernet
```sh
ip link set <eth> up
```
- \<eth> : nom du lien/cable

#### Vérification
```sh
ip  --netns <namespace-name> a : voir les liens
```

### 5. Activation des interfaces dans les namespaces
```sh
ip netns exec <namespace-name> ip link set lo up
ip netns exec <namespace-name> ip link set <peer> up
```
activé les interfaces et l'interface LOOKBACK qui permet d'envoyé les infos à l'extérieur

### 6. Ajout d'ip aux interfaces
```sh
ip netns exec <namespace-name> ip addr add <ip>/<mask> dev <peer>
```
### 7. Création et activation du bridge
```sh
ip link add <bridge-name> type bridge
ip link set <bridge-name> up
```
### 8. Ajouter les vethernet au bridge
```sh
ip link set <eth> master <bridge-name>
```
### 9. Ajout de l'ip du bridge
```sh
ip addr add <ip>:<mask> dev <bridge-name>
```
### 9. Ajout des route du namespace vers le bridge
```sh
ip netns exec <namespace-name> ip route add default via <bridge-ip>
```

### 10. Ip Forwarding (acces externe)
```sh
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -s <bridge-ip>/<mask> ! -o <bridge-name> -j MASQUERADE
```
ts se qui n'est pas du bridge sors du bridge


#### Vérification
```sh
ip  netns exec <namespace-name> ip a # voir les liens au sein du namespace
ip  netns exec <namespace-name> ip r # voir les routes au sein du namespace
ip  netns exec <namespace-name> ping <bride-ip | other-namespace-ip> # Vérif des liens
ip  netns exec <namespace-name> ping < other device out of network> # Vérif du forwarding
```

## définitions
### VEthernet
Liens abstrait entre 2 ports de 2 machines (virtuel ou physique).
### réseau interne
à l'interieur de l'host (127.0.0.1),
c'est lui qui contient les container
### réseau externe
à l'extérieur de l'host
### réseau docker
- chaque container contient son propres réseau interne
## Comment l'host discute avec les networks
### Partique

1. docker ps
2. docker inspect \<name>

    inspecter l'address mac du container
3. ifconfig
    1. voir vethernet
    2. inspecter l'address mac du container
4. docker exec -ti \<name> bash
5. ip a
    1. voir ether
    2. inspecter l'address mac du container (Le mac du .5 == le mac du .2)
### Conclusions
1. le mac du container est relié au vether de l'host via un autre vether


## lancer un container sur un réseau personaliser

1. création d'un réseau : docker network create \<name>
2. laison d'une image à un réseau : docker run --network <network-name> <image>
## vérifier un container sur un réseau personaliser
1. ping le container : ping \<container-name>
2. Vérifier le DNS de l'endDevices ping : 64 bytes from __\<container>.\<network>__
## Docker0
bridge par défault, défini les IPs des autres NetWork.

__/!\\__ le réseau par défault de détient pas de dns, il est donc impossible de ping un partir d'un nom de container sur les containers sans réseau

__IP par Défault du Réseau__ : 172.17.0.0/16

__IP par Défault du Container__ : 172.17.1./24

### personalisation
#### Pourquoi ?
dans un réseau d'entreprise existant, l'ip du docker0 est peut-être déjà utiliser
#### Configuration
path : :/ect/docker/deamon.json

- bip : bridge et range d'IPs du bridge
- mtu : taille des packet
- default-gateway : passerelle par défault
- dns=[] 
##### Deamond
```json
{
  "bip": "10.10.0.1/16"
}
```
## Driver, Modes
### Bridge
driver par défault
    
    Ouverture de Publish conseiller affin
    d'accéder au service via l'ip de l'host sur le port bind.
### Host
Permet d'utiliser directement le réseau de l'hôte

Reviens mapper directement les exposeds sur la machine host.

__/!\\__ host ByPass totalement les bridges
### None
Aucune communication entre l'extérieur et l'intérieur
### Overlay
Communication inter-conainer entre plusieurs machines
### MacVlan
### IPVlan


## Comandes utiles
```
docker exec -ti name bash
apt update
apt install iputils-ping
ip a
ping
apt install net-tools
ifconfig
ifconfig <name>
docker network ls
systemctl restart docker : recharger docker (pour actualiser le deamond.json)


curl ip:port
```
# NameSpace
  Sert a isolé un container d'un autre
  Existent pour le network, les users
## réseau
__/!\\__ l'host ne peut pas ping un namespace de réseau 
### Comandes Utiles
``` bash
ip a
ip link set lo up
ip netns exec <namespace> ip a # voir les ips du namespaces (et leur état)
ip netns exec <namespace> ip link set lo up # mettre UP l'ip lo coorespondant au LOOKBACk

```
donné un accés d'écritures
# Gestion des ports
## Expose
Simple méta-data de déclaration pour l'éxecution de container définit depuis l'images
permet de donner l'acces à un port pour un futur publish

__/!\\__ Une port exposé ne signifie pas qu'une application ecoute sur ce port

## Publish
permet de bind un port de l'host avec un port exporsé du container

# UserID

/!\ seul les UID compte, pas les username
créé un User id dans l'os et dans le container affin de les liés

## Comandes Utiles
```
chown xavki /path/
```
donné un accés d'écritures
```
chmod 777 /path/
```
géré les droits d'accés

# OUTILS de Sécurité
- Clair
- Falco

# Registry
(ex: openssl)
1. créer 3 dossiers 

    ```mkdir /{registry,certs,auth}```
    - certs : certification et clef
    - registry : stockage
    - auth : auth
2. créer un nouveau certs avec clef séparer clefs 
    - mettre un temps de valididté
    - Common Name :
    - __/!\\__ si cert auto-signé ne pas oublié d'ajouté les insecures    dans le daemond 
      ```sudo vim /ect/docker/daemond.json```
      ```json
      {
        "insecure-registries" : ["registry.name.local:port"]
        ...
      }
      ```
      ```sudo vim /ect/host```

      ```ip registry.name.local:port```
3. créer un contenr avec les volumes des registers
    
    ```docker run -d --restart=always --name registry -v /certs:/certs -v /registry/:/registry/ -e REGISTRY_HTTP_ADDR:0.0.0.0:443 -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.name.local.crt -e REGISTRY_HTTP_TLS_KEY=/certs/registry.name.local.key -e REGISTRY_STORAGE_FILESYTEM_ROOTDIRECTORY=/registry/ -p 443:443 registry:2 ```
    - REGISTRY_STORAGE_FILESYTEM_ROOTDIRECTORY : fichier de stockage
    - REGISTRY_HTTP_TLS_KEY : clef de cert
    - REGISTRY_HTTP_TLS_CERTIFICATE : cert

    Verif:
    ```sh
    curl --sSL --insecure https://172.17.0.2/v2/_catalog
    curl --sSL --insecure https://registry.name.local:port/v2/_catalog
    # list vide mais ping faisable
    ```
4. reTag de l'image Docker et push sur le registry
    ```sh
    # création de la nouvelle images
    docker tag path/image:latest registry.name.local/newimage:v1.0.0
    # push l'image automatiquement au sein du registry
    docker push registry.name.local/newimage:v1.0.0

    # Si err X509 : modifier le daemond.json afin d'ajouter les insecure-registries 
    ```
    Verif:
    ```sh
    curl --sSL --insecure https://registry.name.local:port/v2/_catalog
    # list des images detecté dans le registry (ici:"newimage")
    curl --sSL --insecure https://registry.name.local:port/v2/newimage/tags/list
    # list des tags de image du registry
    ```
5. Ajouté des moyens d'authentification
    ```shell
    docker run --entrypoint htpasswd httpd:2 -Bbn myUser myPass > auth/htpasswd

    docker run -d --restart=always --name registry -v /certs:/certs -v /auth/:/auth/ -v /registry/:/registry/ -e REGISTRY_AUTH:htpassws -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd -e REGISTRY_HTTP_ADDR:0.0.0.0:443 -e  REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.name.local.crt -e REGISTRY_HTTP_TLS_KEY=/certs/registry.name.local.key -e REGISTRY_STORAGE_FILESYTEM_ROOTDIRECTORY=/registry/ -p 443:443 registry:2 
    ```
    - REGISTRY_AUTH : méthode/protocol d'identification
    - REGISTRY_AUTH_HTPASSWD_PATH : fichier connextion
    - REGISTRY_AUTH_HTPASSWD_REALM : nom du royaume
    
    Vérif:
    - suprimer les auths de test ```vim ~/.docker/config.json``` ou docker logout registry.name.local
    ```docker tag path/image:latest registry.name.local/newimage:v1.0.0 ```
    ```sh
    docker push registry.name.local
    # marche pas car pas login
    docker login registry.name.local
    # connection effectuer
    docker push registry.name.local
    # marche car login
    ```
    